## ✅ 対応方針（正式設計案）

### 🎯 目的

* `JdbcTemplate#setFetchSize()` の値を、
  `CPM_CMM_CRYPTIC_CONTROL` テーブルの設定値から動的に取得できるようにする。
* 不正値や設定なしの場合は既定値 `10` を使用。
* 一部の業務処理で個別に `FetchSize` を指定可能にする（上書き許可）。

---

## ✅ 新規定数・キー仕様

| 項目           | 内容                             |
| -------------- | -------------------------------- |
| 設定テーブル   | `CPM_CMM_CRYPTIC_CONTROL`        |
| KEY名          | `SOIL_DB_FETCH_SIZE`（推奨）     |
| VALUE          | 数値（10〜任意上限）             |
| 初期値         | 10                               |
| フェイルセーフ | 10未満、不正文字列の場合は10固定 |

---

## ✅ 実装イメージ（変更点）

### ▼ 1. 共通のFetchSize取得ロジックを新設

```java
public class SoilDbConfig {

    private static final int DEFAULT_FETCH_SIZE = 10;
    private static final String FETCH_SIZE_KEY = "SOIL_DB_FETCH_SIZE";

    private static Integer cachedFetchSize = null;

    public static int getFetchSize() {
        // キャッシュされていれば即返す
        if (cachedFetchSize != null) {
            return cachedFetchSize;
        }

        int value = DEFAULT_FETCH_SIZE;
        try {
            String sql = "SELECT VALUE FROM CPM_CMM_CRYPTIC_CONTROL WHERE KEY = ?";
            String str = SoilDbExecutor.getStringResult(sql, new Object[]{FETCH_SIZE_KEY});
            if (str != null && str.matches("\\d+")) {
                value = Integer.parseInt(str);
            }
        } catch (Exception e) {
            // ログだけ出してデフォルト使用
            Logger.warn("Failed to load FetchSize from CPM_CMM_CRYPTIC_CONTROL", e);
        }

        // フェイルセーフ
        if (value < DEFAULT_FETCH_SIZE) {
            value = DEFAULT_FETCH_SIZE;
        }

        cachedFetchSize = value;
        return value;
    }

    public static void clearCache() {
        cachedFetchSize = null;
    }
}
```

* 一度読み込んだ値をキャッシュ（DB負荷防止）
* 必要に応じて `clearCache()` で再読込可能
* `Logger` はSoil既存のログクラスを想定

---

### ▼ 2. SoilDbExecutor 内修正

既存の `query()` メソッドの `jt.setFetchSize(500)` 部分を修正します。

```java
public static void query(String sql, Object[] param, RowCallbackHandler rch) {
    JdbcTemplate jt = jdbc.getUpdateTemplate();

    // 修正箇所 ▼
    int fetchSize = SoilDbConfig.getFetchSize();
    jt.setFetchSize(fetchSize);

    jt.query(sql, param, rch);
}
```

これで、全体共通のFetchSizeが制御テーブルの値に依存するようになります。
（既定値10、設定値変更で反映）

---

### ▼ 3. （余力対応）業務側で上書き指定できるAPI追加

個別業務から「このSQLだけfetchSizeを上書き」したい場合に対応。

```java
public static void query(String sql, Object[] param, RowCallbackHandler rch, Integer customFetchSize) {
    JdbcTemplate jt = jdbc.getUpdateTemplate();

    int fetchSize = (customFetchSize != null && customFetchSize >= 1)
        ? customFetchSize
        : SoilDbConfig.getFetchSize();

    jt.setFetchSize(fetchSize);

    jt.query(sql, param, rch);
}
```

業務コード側：

```java
SoilDbExecutor.query(sql, param, rch, 100);  // このSQLだけ100で実行
```

---

## ✅ 出荷対象（PTF構成）

| ファイル            | 内容                                              |
| ------------------- | ------------------------------------------------- |
| SoilDbConfig.java   | 新規追加（共通設定取得）                          |
| SoilDbExecutor.java | 既存修正（`query()`部分変更、オーバーロード追加） |
| SQLスクリプト       | 初期データ登録用                                  |

```sql
INSERT INTO CPM_CMM_CRYPTIC_CONTROL (KEY, VALUE, COM)
VALUES ('SOIL_DB_FETCH_SIZE', '10', 'SoilDB全体のFetchSize設定値（デフォルト10）');
```

---

## ✅ テスト観点

| 観点              | 期待結果                                                   |
| ----------------- | ---------------------------------------------------------- |
| 1. 通常値         | VALUE=300 → jt.setFetchSize(300) が設定される              |
| 2. 不正値         | VALUE='abc' → fetchSize=10                                 |
| 3. 下限値         | VALUE='0' → fetchSize=10                                   |
| 4. 未登録         | レコードなし → fetchSize=10                                |
| 5. 上書き指定     | query(sql,param,rch,200) で 200 が反映される               |
| 6. キャッシュ確認 | 同一アプリ内で繰り返し取得してもDBアクセスが1回のみ        |
| 7. 回帰           | 旧バージョン呼び出しも動作する（オーバーロードで互換保持） |

---

## ✅ 今後の拡張

* キャッシュのリロードタイミングを、Soilの設定リロード機構（例：`SoilPropertyRefresher`）に統合可。
* JMX経由で動的変更なども将来可能。


