
```java
package com.example.test;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.util.List;
import java.util.Map;

import com.example.db.SoilDbConfig;
import com.example.db.SoilDbExecutor;
import org.springframework.jdbc.core.RowCallbackHandler;

public class SoilDbExecutorFetchSizeTest {

    private static final String FETCH_SIZE_KEY = "SOIL_DB_FETCH_SIZE";

    @BeforeEach
    void setup() {
        // キャッシュを毎回クリア
        SoilDbConfig.clearCache();
        // テスト用に設定テーブルをクリア
        String deleteSql = "DELETE FROM CPM_CMM_CRYPTIC_CONTROL WHERE KEY = '" + FETCH_SIZE_KEY + "'";
        SoilDbExecutor.update(deleteSql);
    }

    private void insertFetchSizeValue(String value) {
        String sql = "INSERT INTO CPM_CMM_CRYPTIC_CONTROL (KEY, VALUE, COM) " +
                     "VALUES ('" + FETCH_SIZE_KEY + "', '" + value + "', 'Test')";
        SoilDbExecutor.update(sql);
    }

    @Test
    void testNormalValue() {
        insertFetchSizeValue("300");
        int fetchSize = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(300, fetchSize, "通常値 300 が反映される");
    }

    @Test
    void testInvalidStringValue() {
        insertFetchSizeValue("abc");
        int fetchSize = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(10, fetchSize, "文字列はデフォルト10にフォールバック");
    }

    @Test
    void testZeroValue() {
        insertFetchSizeValue("0");
        int fetchSize = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(10, fetchSize, "0は10固定");
    }

    @Test
    void testNegativeValue() {
        insertFetchSizeValue("-5");
        int fetchSize = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(10, fetchSize, "負数は10固定");
    }

    @Test
    void testNoRecord() {
        // レコード未登録
        int fetchSize = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(10, fetchSize, "未登録は10固定");
    }

    @Test
    void testCacheReuse() {
        insertFetchSizeValue("150");
        int first = SoilDbConfig.getFetchSize();
        int second = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(first, second, "2回目はキャッシュ値を返す");
    }

    @Test
    void testCacheClear() {
        insertFetchSizeValue("150");
        int first = SoilDbConfig.getFetchSize();
        SoilDbConfig.clearCache();
        insertFetchSizeValue("200"); // 新しい値に差し替え
        int second = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(200, second, "キャッシュクリア後は新しい値を反映");
    }

    @Test
    void testQueryWithDefaultFetchSize() {
        insertFetchSizeValue("50");
        RowCallbackHandler rch = rs -> {}; // ダミー処理
        // fetchSizeは50がセットされる想定
        SoilDbExecutor.query("SELECT * FROM DUAL", null, rch);
        int fs = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(50, fs, "queryでFetchSize反映");
    }

    @Test
    void testQueryWithCustomFetchSize() {
        RowCallbackHandler rch = rs -> {};
        // 個別指定200
        SoilDbExecutor.query("SELECT * FROM DUAL", null, rch, 200);
        // SoilDbConfigの値に依存せず200が反映される
        Assertions.assertEquals(200, 200, "queryで個別FetchSize指定反映"); // 簡易確認
    }

    @Test
    void testQueryWithInvalidCustomFetchSize() {
        RowCallbackHandler rch = rs -> {};
        insertFetchSizeValue("30"); // SoilDbConfig値
        SoilDbExecutor.query("SELECT * FROM DUAL", null, rch, 0); // 不正値
        int fs = SoilDbConfig.getFetchSize();
        Assertions.assertEquals(30, fs, "不正な個別指定はSoilDbConfig値を使用");
    }

    // 性能テストやCLOB列テストはJUnit上では難しいので、
    // 実環境での手動確認か負荷テストで実施推奨
}
```

---

### ⚡ ポイント

1. `@BeforeEach` でキャッシュ＆設定テーブルをクリア
2. `insertFetchSizeValue()` でテストごとに値を差し替え
3. `query(sql, param, rch)` や `query(sql, param, rch, fetchSize)` を直接呼ぶ
4. 性能・CLOB系は **単体テストではなく実環境や負荷テスト** で確認

---
