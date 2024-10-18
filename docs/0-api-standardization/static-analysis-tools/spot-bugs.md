# 【参考】SpotBugsによる静的解析導入ガイド

## 概要
**SpotBugs**は、Javaバイトコードを対象に静的解析を実施し、潜在的なバグやコードの問題を検出するツールです。本ガイドでは、API開発標準化を推進する一環として、SpotBugsをプロジェクトに統合し、コード品質の向上を図るための設定手順および活用方法について説明します。

## 前提条件
- **OS**: Windows
- **IDE**: Visual Studio Code (VSCode)
- **ビルドツール**: Gradle

---

## 設定手順

### 1. Gradleプラグインの追加
まず、プロジェクトの`build.gradle`ファイルにSpotBugsプラグインを追加します。これにより、Gradleを介してSpotBugsを利用可能になります。

```gradle
plugins {
    id 'com.github.spotbugs' version '5.0.14' // SpotBugsのバージョンを指定
}

spotbugs {
    toolVersion = '5.0.14'  // SpotBugsのツールバージョンを設定
    ignoreFailures = false   // バグ検出時にビルドが失敗する設定
}

tasks.withType(SpotBugsTask).configureEach {
    reports {
        xml.enabled = false    // XMLレポートを無効化
        html.enabled = true    // HTMLレポートを有効化し、視覚的な確認を容易に
        html.destination = file("$buildDir/reports/spotbugs/main.html") // 出力先を指定
    }
}
```

### 2. SpotBugsの実行
設定完了後、以下のコマンドを使用してSpotBugsを実行し、コードの静的解析を行います。

```bash
./gradlew spotbugsMain
```

SpotBugsは`build/reports/spotbugs/`にHTMLレポートを生成します。これにより、コードの潜在的な問題を視覚的に確認できます。

---

## カスタマイズ可能なオプション

SpotBugsはプロジェクトに応じてカスタマイズ可能です。プロジェクトの特性に応じて、以下のオプションを利用してSpotBugsの挙動を調整することが推奨されます。

### 1. `ignoreFailures` オプション
デフォルトでは、SpotBugsがバグを検出するとビルドが失敗しますが、検出されたバグを無視してビルドを続行させたい場合は、`ignoreFailures`を`true`に設定することが可能です。

```gradle
spotbugs {
    ignoreFailures = true // 検出されたバグを無視してビルドを続行する
}
```

### 2. レポート形式の設定
SpotBugsは、解析結果を複数の形式（XML、HTML、Text）で出力できます。例えば、HTMLとXML両方の形式でレポートを出力するには以下の設定を追加します。

```gradle
tasks.withType(SpotBugsTask).configureEach {
    reports {
        xml.enabled = true    // XMLレポートを有効化
        html.enabled = true   // HTMLレポートを有効化
        text.enabled = false  // Textレポートを無効化
    }
}
```

### 3. レポートの出力先変更
レポートの出力先は、`html.destination`や`xml.destination`オプションを使用して変更可能です。例えば、HTMLレポートの保存先をカスタマイズするには、以下のように設定します。

```gradle
tasks.withType(SpotBugsTask).configureEach {
    reports {
        html.enabled = true
        html.destination = file("$buildDir/reports/spotbugs/main.html") // 出力先ディレクトリを指定
    }
}
```

### 4. 解析対象のカスタマイズ
SpotBugsは、特定のソースセットやディレクトリを解析対象として指定することができます。例えば、テストコードを解析対象に含める場合、以下のように設定します。

```gradle
tasks.withType(SpotBugsTask).configureEach {
    classes = files("${project.buildDir}/classes/java/test") // テストコードを解析対象に
}
```

---

## SpotBugsルールセットのカスタマイズ

SpotBugsでは、プロジェクト特有の要件に応じて独自のルールセットを作成し、適用することができます。特定のバグタイプやルールを除外したい場合は、フィルタファイルを使用して解析対象を制御します。

### フィルタファイルの設定例
```gradle
spotbugs {
    effort = "max"    // 最大の努力レベルで解析を実施
    reportLevel = "low" // 報告レベルを「low」に設定
    excludeFilter = file("config/spotbugs/exclude.xml") // 除外フィルタファイルを指定
}
```

---

## Visual Studio CodeでのSpotBugs利用

SpotBugsはVisual Studio Code (VSCode) との連携が可能です。以下の拡張機能をインストールすることで、VSCode内で静的解析の結果を確認できます。

- **CheckStyle for Java**: コードスタイルチェックツール
- **SpotBugs for Java**: SpotBugsによる解析サポート
- **Java Language Support**: Java開発サポート拡張機能
