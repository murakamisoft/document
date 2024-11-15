# Spring Boot 3.3.5 と Java 21 へのアップグレード手順と懸念点

## アップグレード手順

### 1. Spring Boot のバージョンをアップグレード

`build.gradle` の Spring Boot プラグインのバージョンを 3.3.5 に変更します。

```groovy
plugins {
    id "org.springframework.boot" version "3.3.5"
}
```

### 2. Gradle のバージョンをアップグレード

Gradle 7.4.2 は Java 21 に対応していないため、Gradle を 8.0 以上にアップグレードします。

`gradle-wrapper.properties` の `distributionUrl` を以下のように変更します。

```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-8.0-bin.zip
```

### 3. Java バージョンの設定

`build.gradle` で Java 21 を使用するために、`sourceCompatibility` と `targetCompatibility` を Java 21 に設定します。

```groovy
sourceCompatibility = '21'
targetCompatibility = '21'
```

また、`build.gradle` の `java` プラグインセクションも確認し、次のように追加します。

```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21)
    }
}
```

### 4. JDK 21 のインストールと設定

ローカル開発環境やビルドサーバーに Java 21 がインストールされていることを確認します。`java -version` コマンドで確認し、必要に応じて Java 21 をインストールします。

### 5. 依存ライブラリの確認と修正

Spring Boot 3.x では Jakarta EE 9+（`javax` → `jakarta`）に対応しています。プロジェクト内で `javax` を使用している場合、`jakarta` に変更する必要があります。

```java
// 例: javax.persistence.Entity → jakarta.persistence.Entity
```

また、Spring Boot 3.x と Java 21 の組み合わせに対応していないライブラリがあれば、それらを最新バージョンにアップグレードします。

### 6. ビルドとテスト

すべての設定を変更した後、次のコマンドでビルドを行い、動作確認をします。

```bash
./gradlew clean build
```

問題がなければ、アプリケーションは Spring Boot 3.3.5 と Java 21 で正常に動作するはずです。

---

## 懸念点

### 1. 互換性の問題
   - **Spring Boot 3.x** は **Java 17以降** と互換性があり、**Java 8** 以前からの移行には注意が必要です。
   - `javax` 名前空間が `jakarta` に変更されているため、プロジェクト内で `javax` を使用している場合、`jakarta` に変更する必要があります。
   - Spring Boot 3.x 以降、**Tomcat 10+** が必要で、Jakarta EE 9 に基づいています。これにより、依存関係のアップグレードが必要です。

### 2. 依存ライブラリの非互換性
   - 使用しているライブラリが Spring Boot 3.x と Java 21 に対応していない場合、最新版にアップグレードする必要があります。
   - **サードパーティ製ライブラリ**の互換性も確認し、必要であればアップグレードを行います。

### 3. JDK 21 の新機能と変更
   - **Java 21 の新機能**（JEP 409: Sealed Interfaces, JEP 421: Pattern Matching for Switch など）はプロジェクトに影響を与える可能性があります。
   - **JDK 21 と Spring Boot 3.x の互換性**を確認し、特にバグや問題がないかを調査する必要があります。

### 4. ビルドとデプロイの問題
   - **Gradle 8.0** ではいくつかの非推奨機能が削除されています。ビルドスクリプトの調整が必要です。
   - Gradle の設定変更後に、ビルドエラーや動作不良が発生しないか確認が必要です。

### 5. パフォーマンスとリソース
   - Java 21 の新しいガーベジコレクションアルゴリズム（例: ZGC, Shenandoah）を使用するには適切な設定が必要です。
   - 新しいガーベジコレクターを使用する場合、パフォーマンステストを実施し、リソースの使用を確認します。

### 6. デバッグとテストの実行
   - アップグレード後の **ユニットテスト** や **統合テスト** が正しく動作するか確認する必要があります。

---

## 対策方法

1. **コードレビューと変更管理**:
   - Jakarta EE への移行やライブラリの更新を慎重に行い、テストを充実させることが重要です。

2. **依存関係の確認と更新**:
   - 使用しているすべてのライブラリが Java 21 および Spring Boot 3.x と互換性があるかを確認し、必要な場合は依存関係を更新します。

3. **パフォーマンステスト**:
   - Java 21 の新しいガーベジコレクターやパフォーマンスの最適化を活用するため、パフォーマンステストを実施し、リソースの使用やアプリケーションの応答性を確認します。

4. **ビルドとデプロイの確認**:
   - Gradle の設定が正しく行われているか、ビルドが正常に動作するか、デプロイに問題がないかを確認します。
