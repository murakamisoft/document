# 開発環境構築手順書

## 目次
[[_TOC_]]

## 概要

本書では、taskApiを開発するための環境構築手順を示します。構築に必要なソフトウェアのインストール手順および設定方法について記述しています。

## 前提条件

- OS: Windows
- インターネット接続が必要。

## 推奨ツール

個人的に使用しているものがあれば、そちらでも可能です。

- IDE: [Visual Studio Code (VSCode)](https://code.visualstudio.com/download)
- SQLツール: [A5M2](https://a5m2.mmatsubara.com/)

## 必要なソフトウェア

以下のソフトウェアが必要です。

| ID  | ソフトウェア        | バージョン |
| --- | ------------------- | ---------- |
| 1   | **Java**            | 8          |
| 2   | **Apache Kafka**    | 2.13-3.7.1 |
| 3   | **Oracle Database** | 19c        |

## 1. Java JDK 8のインストール

1. [Oracle JDKのダウンロードページ](https://www.oracle.com/jp/java/technologies/downloads/#java8-windows)からJDK 8をダウンロードし、指示に従ってインストールを行います。（JDK8のインストールのためには、Oracleのアカウントを作成する必要がありそう。。）
2. 環境変数`JAVA_HOME`をJDKのインストールパスに設定します。
   - 例: `C:\Program Files\Java\jdk1.8.0_xxx`
3. `PATH`環境変数に`%JAVA_HOME%\bin`を追加します。

## 2. Apache Kafkaのインストール

1. [Apache Kafkaのダウンロードページ](https://kafka.apache.org/downloads)からKafka 2.13-3.7.1をダウンロードし、適当なディレクトリに解凍します。
2. 環境変数`KAFKA_HOME`をKafkaの解凍パスに設定します。
   - 例: `C:\kafka_2.13-3.7.1`
3. `PATH`環境変数に`%KAFKA_HOME%\bin\windows`を追加します。

## 3. Oracle Databaseのインストール

1. [Oracle Databaseのダウンロードページ](https://www.oracle.com/jp/database/technologies/oracle-database-software-downloads.html)からOracle Database 19cをダウンロードします。
2. インストール手順に従ってOracle Databaseをインストールします。

## 4. Kafkaのセットアップ

1. **Zookeeperの起動**
   - コマンドプロンプトを開き、以下のコマンドを実行します。
   ```bash
   zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
   ```

2. **Kafkaの起動**
   - 別のコマンドプロンプトを開き、以下のコマンドを実行します。
   ```bash
   kafka-server-start.bat %KAFKA_HOME%\config\server.properties
   ```

## 5. Kafkaの動作確認

1. **トピックの作成**
   - 新しいコマンドプロンプトを開き、以下のコマンドを実行してトピックを作成します。
   ```bash
   kafka-topics.bat --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
   ```

2. **メッセージの送信**
   - 同じコマンドプロンプトで、以下のコマンドを実行してメッセージをトピックに送信します。
   ```bash
   kafka-console-producer.bat --topic test-topic --bootstrap-server localhost:9092
   ```
   - プロンプトが表示されたら、送信したいメッセージを入力し、Enterキーを押します。例えば「Hello Kafka」と入力して送信します。

3. **メッセージの確認**
   - 別のコマンドプロンプトを開き、以下のコマンドを実行してトピックからメッセージを読み取ります。
   ```bash
   kafka-console-consumer.bat --topic test-topic --bootstrap-server localhost:9092 --from-beginning
   ```

## 6. プロジェクトのビルドと実行

1. プロジェクトをビルドするために、ターミナルで以下のコマンドを実行します。
   ```bash
   gradle build
   ```

2. プロジェクトを実行するために、以下のコマンドを実行します。
   ```bash
   gradle run
   ```

以上