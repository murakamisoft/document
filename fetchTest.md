## ✅ SoilDbConfigTest.java（import明示）

```java
package jp.co.xxx.soil.db;

import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;

import static org.junit.jupiter.api.Assertions.assertEquals;

class SoilDbConfigTest {

    @BeforeAll
    static void setupAll() {
        SoilDbConfig.clearCache();
    }

    @BeforeEach
    void setup() {
        SoilDbConfig.clearCache();
    }

    @Test
    @DisplayName("【正常】制御テーブルに数値が設定されている場合、その値がFetchSizeとして返る")
    void testValidValue() {
        try (MockedStatic<SoilDbExecutor> mock = Mockito.mockStatic(SoilDbExecutor.class)) {
            mock.when(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()))
                .thenReturn("300");

            int result = SoilDbConfig.getFetchSize();
            assertEquals(300, result);
        }
    }

    @Test
    @DisplayName("【異常】制御テーブルに不正文字列が設定されている場合はデフォルト値10を返す")
    void testInvalidStringValue() {
        try (MockedStatic<SoilDbExecutor> mock = Mockito.mockStatic(SoilDbExecutor.class)) {
            mock.when(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()))
                .thenReturn("abc");

            int result = SoilDbConfig.getFetchSize();
            assertEquals(10, result);
        }
    }

    @Test
    @DisplayName("【異常】制御テーブルに0や負数などが設定されている場合はデフォルト値10を返す")
    void testInvalidNumericValue() {
        try (MockedStatic<SoilDbExecutor> mock = Mockito.mockStatic(SoilDbExecutor.class)) {
            mock.when(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()))
                .thenReturn("0");

            int result = SoilDbConfig.getFetchSize();
            assertEquals(10, result);
        }
    }

    @Test
    @DisplayName("【異常】制御テーブルに値が存在しない場合はデフォルト値10を返す")
    void testNullValue() {
        try (MockedStatic<SoilDbExecutor> mock = Mockito.mockStatic(SoilDbExecutor.class)) {
            mock.when(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()))
                .thenReturn(null);

            int result = SoilDbConfig.getFetchSize();
            assertEquals(10, result);
        }
    }

    @Test
    @DisplayName("【例外】SoilDbExecutorで例外が発生してもデフォルト値10でフェイルセーフ動作")
    void testExceptionFallback() {
        try (MockedStatic<SoilDbExecutor> mock = Mockito.mockStatic(SoilDbExecutor.class)) {
            mock.when(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()))
                .thenThrow(new RuntimeException("DB接続エラー"));

            int result = SoilDbConfig.getFetchSize();
            assertEquals(10, result);
        }
    }

    @Test
    @DisplayName("【キャッシュ】2回目呼び出しではDBアクセスが呼ばれない")
    void testCacheBehavior() {
        try (MockedStatic<SoilDbExecutor> mock = Mockito.mockStatic(SoilDbExecutor.class)) {
            mock.when(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()))
                .thenReturn("200");

            int first = SoilDbConfig.getFetchSize();
            assertEquals(200, first);

            int second = SoilDbConfig.getFetchSize();
            assertEquals(200, second);

            mock.verify(() -> SoilDbExecutor.getStringResult(Mockito.anyString(), Mockito.any()), Mockito.times(1));
        }
    }
}
```

---

## ✅ SoilDbExecutorQueryTest.java（import明示）

```java
package jp.co.xxx.soil.db;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowCallbackHandler;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

class SoilDbExecutorQueryTest {

    @Test
    @DisplayName("【カスタムFetchSize指定】customFetchSizeが指定された場合はその値を優先")
    void testCustomFetchSize() {
        JdbcTemplate mockTemplate = mock(JdbcTemplate.class);
        SoilJdbc mockJdbc = mock(SoilJdbc.class);
        when(mockJdbc.getUpdateTemplate()).thenReturn(mockTemplate);

        try (MockedStatic<SpringContext> springCtx = Mockito.mockStatic(SpringContext.class);
             MockedStatic<SoilDbConfig> configMock = Mockito.mockStatic(SoilDbConfig.class)) {

            springCtx.when(() -> SpringContext.getBean("soilJdbc")).thenReturn(mockJdbc);
            configMock.when(SoilDbConfig::getFetchSize).thenReturn(10);

            RowCallbackHandler mockRch = mock(RowCallbackHandler.class);

            SoilDbExecutor.query("SELECT * FROM DUAL", new Object[]{}, mockRch, 200);

            verify(mockTemplate).setFetchSize(200);
            verify(mockTemplate).query(anyString(), any(Object[].class), eq(mockRch));
        }
    }
}
```

---

### ✅ これでOKなポイント

* `import *` 一切なし
* `JUnit 5` (`org.junit.jupiter.*`)
* `Mockito` の static モックを使用（`MockedStatic`）
* どのクラスを参照しているか明確
* Eclipseでもすぐ動く構成

