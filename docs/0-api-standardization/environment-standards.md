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

以上