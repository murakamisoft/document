# JaCoCoの設定ガイド

## 目次
[[_TOC_]]

## 目的
JaCoCo（Java Code Coverage）は、ユニットテストやインテグレーションテストのカバレッジを計測し、コードのテスト品質を向上させることを目的としています。このガイドでは、GradleプロジェクトにおけるJaCoCoの設定方法およびレポート出力の手順について説明します。

## 前提条件
- **OS**: Windows
- **IDE**: Visual Studio Code (VSCode)
- **ビルドツール**: Gradle

---

## 設定手順

### 1. Gradleプラグインの追加
プロジェクトの`build.gradle`ファイルにJaCoCoプラグインを追加します。このプラグインを適用することで、テスト時に自動的にコードカバレッジが計測されます。

#### `build.gradle`の設定:
```gradle
plugins {
    id 'jacoco' // JaCoCoプラグインを適用
}

jacoco {
    toolVersion = "0.8.8" // JaCoCoのバージョンを指定
}

tasks.jacocoTestReport {
    reports {
        xml.required = true // XMLレポートの出力を有効化
        csv.required = false // CSVレポートの出力を無効化
        html.outputLocation = layout.buildDirectory.dir('jacocoHtml') // HTMLレポートの出力先
    }
}

tasks.test {
    finalizedBy(tasks.jacocoTestReport) // テスト実行後にレポートを生成
}
```

### 2. JaCoCoの実行
設定が完了したら、以下のコマンドを実行してJaCoCoを使用したテストカバレッジ計測とレポート生成を行います。

```bash
./gradlew test jacocoTestReport
```

このコマンドを実行すると、`build/reports/jacoco/test/html/`ディレクトリにHTML形式のカバレッジレポートが生成されます。これにより、テスト対象コードのどの部分がカバーされているかを視覚的に確認できます。

---

## JaCoCoのカスタマイズ

JaCoCoは、プロジェクトやチームの要件に合わせて柔軟にカスタマイズ可能です。以下にいくつかのカスタマイズ方法を示します。

### 1. カバレッジレポートの形式
JaCoCoは、HTML、XML、CSVの3つの形式でレポートを生成できます。デフォルトではHTML形式が有効ですが、必要に応じて他の形式も有効化できます。

#### `build.gradle`例:
```gradle
tasks.jacocoTestReport {
    reports {
        xml.required = true // XMLレポートを有効化
        html.required = true // HTMLレポートを有効化
        csv.required = false // CSVレポートを無効化
    }
}
```

### 2. カバレッジ基準の設定
テストのカバレッジ基準を設定し、一定の基準を満たさない場合にはビルドを失敗させることができます。以下は、インストラクションカバレッジやブランチカバレッジの基準を設定する例です。

#### `build.gradle`例:
```gradle
jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                counter = 'INSTRUCTION'
                value = 'COVEREDRATIO'
                minimum = 0.80 // インストラクションカバレッジが80%未満の場合はビルド失敗
            }
        }
        rule {
            limit {
                counter = 'BRANCH'
                value = 'COVEREDRATIO'
                minimum = 0.70 // ブランチカバレッジが70%未満の場合はビルド失敗
            }
        }
    }
}

tasks.check.dependsOn tasks.jacocoTestCoverageVerification // カバレッジチェックを追加
```

### 3. ソースファイルの除外
特定のクラスやパッケージをカバレッジレポートの対象から除外することができます。

#### `build.gradle`例:
```gradle
jacocoTestReport {
    afterEvaluate {
        classDirectories.setFrom(files(classDirectories.files.collect {
            fileTree(dir: it, exclude: ['com/example/excluded/**'])
        }))
    }
}
```

---

## VSCodeでのJaCoCo活用

VSCode上でJaCoCoを活用するためには、以下の拡張機能を使用します。

1. **Test Runner for Java** - VSCode内でJavaのテストを実行でき、JaCoCoのカバレッジレポートも確認可能です。
2. **Java Language Support** - Java開発環境の拡張機能。

これにより、VSCode内で簡単にテストを実行し、カバレッジをチェックすることができます。

---

## JaCoCoレポートの利用

JaCoCoレポートを活用することで、以下の点に役立ちます。

- **テストの網羅性の確認**: テストが不足している箇所を特定し、より堅牢なテストケースを作成できます。
- **テストの品質向上**: カバレッジ基準を設定し、テスト品質を自動的に管理することで、コードのバグを未然に防ぐことが可能です。
