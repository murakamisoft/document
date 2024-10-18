# 静的解析ツールによる品質向上ガイド

## 前提条件
- **OS**: Windows
- **IDE**: Visual Studio Code (VSCode)
- **プロジェクト管理**: Gradle

## 目的
このガイドでは、Javaプロジェクトにおける静的解析ツールを活用し、コードの品質向上やバグの早期発見を図ることを目的とします。これにより、API開発の標準化を促進し、メンテナビリティの高いコードベースを維持します。

## 使用ツール
1. [SpotBugs](https://spotbugs.github.io/)
2. [Checkstyle](https://checkstyle.sourceforge.io/)
3. [JaCoCo](https://www.jacoco.org/jacoco/)
4. Gradleの**Project Report**

### 1. SpotBugsの導入
SpotBugsは、Javaバイトコードを解析して潜在的なバグや問題を検出します。

#### 設定手順
1. Gradleファイル（`build.gradle`）に以下のプラグインを追加します。
    ```gradle
    plugins {
        id 'com.github.spotbugs' version '5.0.14'
    }

    spotbugs {
        toolVersion = '5.0.14'
        ignoreFailures = true
    }

    tasks.withType(SpotBugsTask).configureEach {
        reports {
            xml.enabled = false
            html.enabled = true
        }
    }
    ```
2. ターミナルで以下を実行してSpotBugsを実行します。
    ```bash
    ./gradlew spotbugsMain
    ```

#### 出力結果
HTML形式でバグレポートが生成され、コードに潜む潜在的なバグを視覚的に確認できます。

---

### 2. Checkstyleの導入
Checkstyleは、コーディング規約やフォーマットのチェックを行うツールです。

#### 設定手順
1. GradleファイルにCheckstyleプラグインを追加します。
    ```gradle
    plugins {
        id 'checkstyle'
    }

    checkstyle {
        toolVersion = '10.0'
        configFile = file('config/checkstyle/checkstyle.xml')
    }

    tasks.withType(Checkstyle).configureEach {
        reports {
            xml.enabled = false
            html.enabled = true
        }
    }
    ```
2. ルールファイル（`checkstyle.xml`）をプロジェクトの`config/checkstyle`ディレクトリに配置します。

3. ターミナルで以下を実行してCheckstyleを実行します。
    ```bash
    ./gradlew checkstyleMain
    ```

#### 出力結果
Checkstyleによって生成されるレポートには、コードのフォーマット違反箇所が表示されます。

---

### 3. JaCoCoの導入
JaCoCoは、テストのカバレッジを測定するためのツールです。

#### 設定手順
1. GradleファイルにJaCoCoプラグインを追加します。
    ```gradle
    plugins {
        id 'jacoco'
    }

    jacoco {
        toolVersion = "0.8.7"
    }

    tasks.withType(Test).configureEach {
        finalizedBy jacocoTestReport
    }

    jacocoTestReport {
        reports {
            xml.enabled = true
            html.enabled = true
        }
    }
    ```
2. ターミナルで以下を実行してテストカバレッジを計測します。
    ```bash
    ./gradlew test jacocoTestReport
    ```

#### 出力結果
生成されたレポートには、テストコードがどの程度のコードをカバーしているかが表示されます。これにより、テストが不十分な部分を明確に把握できます。

---

### 4. Project Reportの利用
GradleのProject Reportプラグインを使用して、プロジェクトの依存関係やタスク一覧をレポートとして出力します。

#### 設定手順
1. Gradleファイルに以下を追加します。
    ```gradle
    plugins {
        id 'project-report'
    }
    ```

2. ターミナルで以下を実行して依存関係やタスクのレポートを生成します。
    ```bash
    ./gradlew htmlDependencyReport
    ```

#### 出力結果
HTML形式でプロジェクト全体の依存関係やタスクリストが出力され、プロジェクトの構造を把握できます。
