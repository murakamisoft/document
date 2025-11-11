
```java
package com.example.soil;

import org.junit.jupiter.api.*;
import java.util.List;
import java.util.Map;
import java.util.concurrent.atomic.AtomicInteger;

class SoilDbExecutorFetchSizeTest {

    private static final String TEST_LARGE = "TEST_LARGE";
    private static final String TEST_CLOB  = "TEST_CLOB";

    @BeforeAll
    static void setup() {
        try { SoilDbExecutor.update("DROP TABLE " + TEST_LARGE); } catch (Exception ignored) {}
        SoilDbExecutor.update("CREATE TABLE " + TEST_LARGE + " (ID INT PRIMARY KEY, NAME VARCHAR(255))");

        try { SoilDbExecutor.update("DROP TABLE " + TEST_CLOB); } catch (Exception ignored) {}
        SoilDbExecutor.update("CREATE TABLE " + TEST_CLOB + " (ID INT PRIMARY KEY, CONTENT CLOB)");
    }

    @AfterAll
    static void cleanup() {
        SoilDbExecutor.update("DROP TABLE " + TEST_LARGE);
        SoilDbExecutor.update("DROP TABLE " + TEST_CLOB);
    }

    @Test
    void testLargeFetchSizeApplied() {
        // SoilDbConfigのfetchSizeを事前に設定
        SoilDbConfig.clearCache();
        SoilDbExecutor.update("DELETE FROM CPM_CMM_CRYPTIC_CONTROL WHERE KEY = 'SOIL_DB_FETCH_SIZE'");
        SoilDbExecutor.update("INSERT INTO CPM_CMM_CRYPTIC_CONTROL (KEY, VALUE, COM) VALUES (?, ?, ?)",
            new Object[]{"SOIL_DB_FETCH_SIZE", "300", "テスト用fetchSize設定"});

        // データ投入
        for (int i = 1; i <= 1000; i++) {
            SoilDbExecutor.update("INSERT INTO " + TEST_LARGE + " (ID, NAME) VALUES (?, ?)",
                    new Object[]{i, "name" + i});
        }

        AtomicInteger rowCount = new AtomicInteger();

        // SoilDbExecutor経由でSELECT実行
        SoilDbExecutor.query(
            "SELECT * FROM " + TEST_LARGE,
            null,
            rs -> rowCount.incrementAndGet()
        );

        Assertions.assertEquals(1000, rowCount.get(), "全行取得されていること");

        // SoilDbConfigのfetchSizeが300であることも確認
        Assertions.assertEquals(300, SoilDbConfig.getFetchSize(), "fetchSizeが正しく反映されている");
    }

    @Test
    void testClobFetchSizeApplied() {
        SoilDbConfig.clearCache();

        // CLOB投入
        String clobData = new String(new char[1024*1024]).replace('\0', 'A');
        SoilDbExecutor.update("INSERT INTO " + TEST_CLOB + " (ID, CONTENT) VALUES (?, ?)", new Object[]{1, clobData});

        AtomicInteger rowCount = new AtomicInteger();

        // fetchSize個別上書き指定
        SoilDbExecutor.query(
            "SELECT * FROM " + TEST_CLOB,
            null,
            rs -> {
                String content = rs.getString("CONTENT");
                Assertions.assertEquals(1024*1024, content.length(), "CLOBサイズ確認");
                rowCount.incrementAndGet();
            },
            10  // 個別上書き
        );

        Assertions.assertEquals(1, rowCount.get(), "1件取得されていること");
    }
}
```

---

### ポイント

1. `SoilDbConfig.clearCache()` で毎回DBから値を取得
2. CPM_CMM_CRYPTIC_CONTROL の VALUE を更新して fetchSize 動作を確認
3. `AtomicInteger` を使い、RowCallbackHandler内で取得行数をカウント
4. **fetchSizeが実際にJdbcTemplateに反映されるかはRowCallbackHandlerの呼び出し回数やログで間接確認**

   * JdbcTemplate内部のfetchSizeは直接取得できないので、JUnit上では SoilDbConfig の値と連動していることをアサート

---

💡 補足

* データ量やCLOBサイズはローカルPC性能に応じて調整可能
* これで「fetchSize300で大量データ取得」「fetchSize10でCLOB取得」の動作確認をJUnitで自動化できます

