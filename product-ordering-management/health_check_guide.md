# Spring Boot Actuator を使用したヘルスチェックガイド

注文管理システムのAPI設計において、アプリケーションの正常性を確認するために、Spring Boot Actuatorを使用してヘルスチェックAPIを導入する。

---

## 1. Spring Boot Actuator の導入

### 依存関係の追加
プロジェクトの`build.gradle`に以下の依存関係を追加する。

```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

---

## 2. デフォルトのヘルスチェック

Spring Boot Actuatorを導入すると、デフォルトで以下のヘルスチェックAPIが使用可能である。

- **エンドポイント:** `/actuator/health`
- **HTTPメソッド:** GET

### 実行例

アプリケーションを起動後、以下にアクセスする。

```bash
curl http://localhost:8080/actuator/health
```

**レスポンス例:**
```json
{
  "status": "UP"
}
```

アプリケーションが正常であれば、`"status": "UP"` が返される。

---

## 3. Actuatorエンドポイントのカスタマイズ

### application.yml での設定
必要に応じて、`application.yml`または`application.properties`でActuatorの設定を変更する。

#### すべてのエンドポイントを有効化する設定

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*" # すべてのエンドポイントを公開
```

---

## 4. カスタムヘルスチェックの追加

アプリケーション独自のロジックを含むヘルスチェックを追加するには、`HealthIndicator`インターフェースを実装する。

### カスタムヘルスチェックの実装例

以下は、データベース接続状況を確認するカスタムヘルスチェックの例である。

```java
package com.example.ordersystem.health;

import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class DatabaseHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        // データベース接続のチェックロジックを追加
        boolean databaseUp = checkDatabaseConnection();

        if (databaseUp) {
            return Health.up().withDetail("database", "Connected").build();
        } else {
            return Health.down().withDetail("database", "Not Connected").build();
        }
    }

    private boolean checkDatabaseConnection() {
        // データベース接続チェックのダミー実装（本番用に修正）
        return true; // 接続成功を仮定
    }
}
```

### カスタムヘルスチェックのレスポンス例

カスタムヘルスチェックが追加された場合、`/actuator/health`のレスポンスが次のように拡張される。

```json
{
  "status": "UP",
  "components": {
    "databaseHealthIndicator": {
      "status": "UP",
      "details": {
        "database": "Connected"
      }
    }
  }
}
```

---

## 5. セキュリティ設定

### Actuatorエンドポイントのアクセス制御

Actuatorのエンドポイントはデフォルトで外部からアクセス可能である。実運用環境ではセキュリティを強化することを推奨する。

#### application.yml での制限例

特定のエンドポイントのみ公開する:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "health,info" # 必要なエンドポイントのみ公開
```

詳細を含めてヘルスチェック結果を表示する設定:

```yaml
management:
  endpoint:
    health:
      show-details: "always"
```

### Spring Security で保護

`spring-boot-starter-security`を導入することで、Actuatorエンドポイントに認証を追加できる。（注文管理では認証は使用しない。）

---

## 6. ヘルスチェックの運用例

注文管理システムで、以下の観点をヘルスチェックに組み込むことを推奨する。

1. **データベース接続状況**: 
   - データベースが利用可能か確認する。

2. **外部APIの連携状況**:
   - 連携先APIが応答するかチェックする。

3. **アプリケーション内部状態**:
   - キューのサイズやバックグラウンドジョブの状況を監視する。

### 実例

ヘルスチェックに複数の要素を組み込む例:

```java
@Component
public class OrderSystemHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        boolean databaseUp = checkDatabaseConnection();
        boolean externalApiUp = checkExternalApi();

        if (databaseUp && externalApiUp) {
            return Health.up()
                .withDetail("database", "Connected")
                .withDetail("externalApi", "Available")
                .build();
        } else {
            return Health.down()
                .withDetail("database", databaseUp ? "Connected" : "Not Connected")
                .withDetail("externalApi", externalApiUp ? "Available" : "Unavailable")
                .build();
        }
    }

    private boolean checkDatabaseConnection() {
        // データベース接続確認ロジック
        return true;
    }

    private boolean checkExternalApi() {
        // 外部APIの接続確認ロジック
        return true;
    }
}
```

---

