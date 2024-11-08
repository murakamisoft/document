# AsyncAPI 導入手順 (Java 8, Spring Boot, Spring Kafka)

## 1. AsyncAPI Generatorのインストール

AsyncAPI GeneratorはNode.jsパッケージとして提供されています。まずNode.jsをインストールし、その後AsyncAPI Generatorをインストールします。

```bash
npm install -g @asyncapi/generator
```

## 2. Gradle設定にAsyncAPIライブラリの追加

AsyncAPI用の依存関係は生成したコードと共に利用しますが、Kafkaの依存関係は`build.gradle`に追加しておきます。

```gradle
implementation 'org.springframework.kafka:spring-kafka'
```

## 3. AsyncAPI仕様ファイルの作成

AsyncAPIの仕様ファイルを`asyncapi.yaml`として作成し、Kafkaイベントに対応するチャンネルを記述します。

```yaml
asyncapi: '2.0.0'
info:
  title: 'Kafka Event API'
  version: '1.0.0'
servers:
  development:
    url: 'localhost:9092'
    protocol: 'kafka'
channels:
  user-created:
    description: 'User created event'
    subscribe:
      message:
        contentType: 'application/json'
        payload:
          type: object
          properties:
            id:
              type: string
            name:
              type: string
```

## 4. コード生成

作成した`asyncapi.yaml`を元に、AsyncAPI Generatorでコードを生成します。Spring用テンプレートを使い、Kafkaのリスナーやプロデューサーのコードを生成します。

```bash
ag asyncapi.yaml @asyncapi/java-spring-template -o output-directory
```

## 5. Spring BootとKafkaの設定

Spring Bootプロジェクトの`application.yml`または`application.properties`にKafka設定を追加します。

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: myGroup
      auto-offset-reset: earliest
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
```

## 6. 生成されたコードの統合とカスタマイズ

生成されたコードをプロジェクトに統合し、必要に応じてカスタマイズします。例えば、`UserCreatedListener`クラスを作成し、Kafkaのメッセージを受信するロジックを追加します。

```java
@Component
public class UserCreatedListener {

    @KafkaListener(topics = "user-created", groupId = "myGroup")
    public void listen(UserCreatedEvent event) {
        // イベント処理のロジック
    }
}
```

## 7. 動作確認

アプリケーションを起動し、Kafkaのトピックに対してイベントが送受信されることを確認します。
