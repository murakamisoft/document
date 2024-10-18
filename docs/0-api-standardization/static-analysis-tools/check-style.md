# Checkstyleの設定ガイド

## 概要
**Checkstyle**は、Javaコードのスタイルや品質を自動的にチェックし、プロジェクト全体でコード規約を統一するためのツールです。本ガイドでは、Gradleを使用したCheckstyleの導入手順および設定方法について説明します。

## 前提条件
- **OS**: Windows
- **IDE**: Visual Studio Code (VSCode)
- **ビルドツール**: Gradle

---

## 設定手順

### 1. Gradleプラグインの追加
まず、プロジェクトの`build.gradle`ファイルにCheckstyleプラグインを追加します。これにより、GradleでCheckstyleの実行が可能になります。

#### `build.gradle`の設定:
```gradle
plugins {
    id 'checkstyle' // Checkstyleプラグインを適用
}

checkstyle {
    toolVersion = '10.0' // Checkstyleのバージョンを指定
    configFile = file("${project.rootDir}/config/checkstyle/checkstyle.xml") // 設定ファイルのパス
}

tasks.withType(Checkstyle).configureEach {
    reports {
        xml.required.set(false) // XMLレポートを無効化
        html.required.set(true) // HTMLレポートを有効化
    }
}
```

### 2. Checkstyle設定ファイル（checkstyle.xml）の作成
Checkstyleは、カスタマイズ可能なルールセットを使用してコードをチェックします。デフォルトのルールセットを使用することも可能ですが、プロジェクト独自のスタイルを定義する場合は、`checkstyle.xml`を作成します。

#### `checkstyle.xml` の例:
以下は、基本的な`checkstyle.xml`の例です。プロジェクトのルートディレクトリに`config/checkstyle/`フォルダを作成し、その中に保存します。

```xml
<?xml version="1.0"?>
<!DOCTYPE module PUBLIC
    "-//Checkstyle//DTD Checkstyle Configuration 1.3//EN"
    "https://checkstyle.org/dtds/configuration_1_3.dtd">

<module name="Checker">
    <module name="TreeWalker">
        <module name="JavadocMethod"/>
        <module name="WhitespaceAfter"/>
        <module name="WhitespaceAround"/>
        <module name="LineLength">
            <property name="max" value="120"/> <!-- 1行あたりの最大文字数 -->
        </module>
        <module name="AvoidStarImport"/> <!-- ワイルドカードインポートの禁止 -->
        <module name="FinalLocalVariable"/>
    </module>
</module>
```

### 3. Checkstyleの実行
設定が完了したら、以下のコマンドを使用してCheckstyleを実行します。

```bash
./gradlew checkstyleMain
```

実行後、Checkstyleレポートが`build/reports/checkstyle/`にHTML形式で出力されます。これにより、コードスタイルの違反箇所を視覚的に確認できます。

---

## Checkstyleのカスタマイズ

Checkstyleは、プロジェクトの要件に応じてルールを柔軟にカスタマイズできます。以下は、よく使用されるルールとそのカスタマイズ例です。

### 1. 行の長さ制限
デフォルトでは、1行あたりの文字数制限は80文字となっていますが、これをプロジェクトに合わせて変更することができます。

```xml
<module name="LineLength">
    <property name="max" value="120"/> <!-- 1行あたりの最大文字数を120に設定 -->
</module>
```

### 2. Javadocコメントのチェック
メソッドやクラスに対するJavadocコメントの存在をチェックする場合、`JavadocMethod`や`JavadocType`を使用します。

```xml
<module name="JavadocMethod"/> <!-- メソッドに対するJavadocコメントのチェック -->
<module name="JavadocType"/>   <!-- クラスに対するJavadocコメントのチェック -->
```

### 3. インデントの統一
インデントのスタイル（スペースまたはタブ）や幅を指定して統一することが可能です。

```xml
<module name="Indentation">
    <property name="basicOffset" value="4"/> <!-- インデント幅を4スペースに設定 -->
</module>
```

### 4. ワイルドカードインポートの禁止
ワイルドカードインポート（例: `import java.util.*;`）を禁止し、明示的なインポートを促すことができます。

```xml
<module name="AvoidStarImport"/> <!-- ワイルドカードインポートを禁止 -->
```

---

## VSCodeでのCheckstyle利用

CheckstyleはVisual Studio Code (VSCode) でも利用可能です。以下の拡張機能を導入することで、VSCode上でコードスタイルのチェックがリアルタイムに行えます。

1. **Checkstyle for Java** - CheckstyleによるコードスタイルチェックをVSCodeでサポートします。
2. **Java Language Support** - Java開発環境の拡張機能。

これらの拡張機能を利用し、コード記述時にリアルタイムでスタイル違反を指摘することで、効率的に開発が進められます。

---

## Checkstyleルールの拡張
Checkstyleのルールセットは、標準で提供されるものに加えて、プロジェクト固有のルールを追加することも可能です。ルールを柔軟に拡張し、コードの品質向上に役立てることができます。
