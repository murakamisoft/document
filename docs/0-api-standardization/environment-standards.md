# 開発環境構築手順書

## 目次
[[_TOC_]]

## 1. 概要
本手順書では、Windows環境におけるtaskAPIの開発に必要なソフトウェアのインストールと設定手順について説明する。

## 2. 前提条件
- OS: Windows
- インターネット接続が必要。

## 3. 技術スタック
以下のソフトウェアを使用する。

| ID  | ソフトウェア        | バージョン     |
| --- | ------------------- | -------------- |
| 1   | **Java**            | 8              |
| 2   | **Apache Kafka**    | 2.8.0          |
| 3   | **Spring Boot**     | 2.3.12.RELEASE |
| 4   | **Spring Kafka**    | 2.6.10         |
| 5   | **MyBatis**         | 3.5.7          |
| 6   | **Gradle**          | 6.8.3          |
| 7   | **Oracle Database** | 19c            |
| 8   | **Debezium**        | 1.6.0.Final    |


## 4. インストール手順

### 4.1. Apache Kafkaのインストール
1. [Apache Kafkaのダウンロードページ](https://kafka.apache.org/downloads)にアクセスする。
2. 最新安定版のKafkaをダウンロードする。
3. ダウンロードしたZIPファイルを解凍し、任意のフォルダに配置する。

### 4.2. Zookeeperのインストール
1. [Apache Zookeeperのダウンロードページ](https://zookeeper.apache.org/releases.html)にアクセスする。
2. 最新安定版のZookeeperをダウンロードする。
3. ダウンロードしたZIPファイルを解凍し、任意のフォルダに配置する。

### 4.3. Java Development Kitのインストール
1. [OracleのJDKダウンロードページ](https://www.oracle.com/java/technologies/javase/jdk21-archive-downloads.html)にアクセスする。
2. JDK 21をダウンロードし、インストールする。

### 4.4. Gradleのインストール
1. [Gradleのダウンロードページ](https://gradle.org/install/)にアクセスする。
2. 最新版のGradleをダウンロードし、インストールする。

### 4.5. Oracle Databaseのインストール
1. [Oracle Databaseのダウンロードページ](https://www.oracle.com/database/technologies/)にアクセスする。
2. Oracle Database 21cをダウンロードし、インストールする。

## 5. 開発ツール
- **Visual Studio Code**を開発ツールとして使用する。
- VSCodeをインストールし、必要な拡張機能（Java Extension PackやSpring Boot Extension Packなど）を追加する。

## 6. 確認手順
すべてのソフトウェアが正しくインストールされたことを確認するために、以下のコマンドを実行する。

```bash
kafka-topics --version
java -version
gradle -v
```

以上。