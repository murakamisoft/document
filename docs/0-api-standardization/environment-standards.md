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

| ID  | ソフトウェア             | バージョン | 互換性の概要                                                                                   |
| --- | ------------------------ | ---------- | ---------------------------------------------------------------------------------------------- |
| 1   | **Apache Kafka**         | 3.5.1      | Zookeeper 3.8.xと互換性があり、Spring Boot、Spring Kafkaとも互換性がある。                     |
| 2   | **Zookeeper**            | 3.8.1      | Kafka 3.5.1と互換性があり、Kafkaの動作に必須である。                                           |
| 3   | **Java Development Kit** | 21         | Spring Boot 3.2.x、Kafka 3.5.x、MyBatis 3.5.xと互換性がある。                                  |
| 4   | **Spring Boot**          | 3.2.x      | Kafka 3.5.x、Spring Kafka 3.1.x、Java 21と互換性がある。                                       |
| 5   | **Spring Kafka**         | 3.1.x      | Spring Boot 3.2.x、Kafka 3.5.xと互換性がある。                                                 |
| 6   | **MyBatis**              | 3.5.7      | Oracle Database 21cと互換性があり、Java 21とも動作する。                                       |
| 7   | **Gradle**               | 8.2        | すべての依存関係のビルドと管理が可能であり、Java 21とも互換性がある。                          |
| 8   | **Oracle Database**      | 21c        | JDBCドライバを使用してJavaアプリケーションと接続でき、Java 21、MyBatis 3.5.7とも互換性がある。 |

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