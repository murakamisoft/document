# APログ出力設計書

## 概要
注文管理APIにおけるログ出力設計について記載する。システムの可観測性向上、障害発生時の迅速な対応、及びシステムの動作確認を目的とする。

## ログの種類
XXX

## ログ出力定義
XXX

## ログ出力ライブラリ
ログ出力にはLogbackを使用。

## ログフォーマット
- **形式**: Logbackのパターンフォーマットを使用して出力する。
- **サンプルフォーマット**:
  ```
  %d{yyyy-MM-dd'T'HH:mm:ss.SSSZ} [%thread] %-5level %logger{36} - %msg%n
  ```

- **サンプルログ**:
  ```
  2025-01-10T12:34:56.789+0900 [main] INFO  com.example.orders.OrderService - 注文登録開始 - 注文ID: 12345, カスタマID: 67890
  2025-01-10T12:34:57.123+0900 [main] INFO  com.example.orders.OrderService - 注文登録完了 - 注文ID: 
  ```

## Logbackを使用した実装例
### 1. Logbackの設定ファイル例（`logback.xml`）
```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd'T'HH:mm:ss.SSSZ} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/application.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/application.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd'T'HH:mm:ss.SSSZ} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <logger name="com.example.orders" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
    </logger>
    
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```

### 2. Javaコードでのログ出力例
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class OrderService {
    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    public void registerOrder(String orderId, String customerId) {
        try {
            logger.info("注文登録開始 - 注文ID: {}, カスタマID: {}", orderId, customerId);
            
            // 業務処理ロジック
            // 例外が発生する可能性のある処理
            
            logger.info("注文登録完了 - 注文ID: {}", orderId);
        } catch (Exception e) {
            logger.error("注文登録失敗 - 注文ID: {}, エラー: {}", orderId, e.getMessage(), e);
        }
    }
}
```

### 3. ログ出力例
#### 正常終了時
```
2025-01-10T12:45:30.123+0900 [main] INFO  com.example.orders.OrderService - 注文登録開始 - 注文ID: 12345, カスタマID: 67890
2025-01-10T12:45:30.456+0900 [main] INFO  com.example.orders.OrderService - 注文登録完了 - 注文ID: 12345
```

#### エラー発生時
```
2025-01-10T12:46:00.789+0900 [main] ERROR com.example.orders.OrderService - 注文登録失敗 - 注文ID: 12345, エラー: Database connection failed
java.sql.SQLException: Database connection failed
    at com.example.orders.OrderService.registerOrder(OrderService.java:25)
    ...
```

## 注意事項
1. **個人情報管理**:
   - ログに個人情報を記載しない。ただし、必要な場合は暗号化またはマスキングを行う。
1. **ログローテーション**:
   - ファイル出力時には定期的なローテーションを実施し、過去ログは★セキュリティ要件に従い保存する。

