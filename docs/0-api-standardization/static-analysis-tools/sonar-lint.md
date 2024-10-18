# SonarLint 設定ガイド

## 目次
[[_TOC_]]

## 前提条件
- **OS**: Windows
- **IDE**: Visual Studio Code (VSCode)

## 目的
このガイドでは、SonarLintを使用してリアルタイムにコードの品質を向上させるための設定方法を説明します。SonarLintは、潜在的なバグやコードの問題を早期に検出し、開発プロセスの改善に寄与します。

## SonarLintの導入手順

### 1. VSCodeにSonarLintをインストール

1. Visual Studio Codeを開きます。
2. 左側の拡張機能アイコン（四角が4つ並んでいるアイコン）をクリックします。
3. 検索バーに「SonarLint」と入力し、表示されたリストからSonarLintを選択します。
4. 「インストール」ボタンをクリックします。

### 2. SonarQube/SonarCloudアカウントの作成（オプション→必要なし）

- SonarQubeまたはSonarCloudにサインアップし、必要に応じてプロジェクトを作成します。

### 3. SonarLintの設定

#### 3.1 SonarQube/SonarCloudに接続する

1. VSCodeで、任意のJavaプロジェクトを開きます。
2. プロジェクト内で右クリックし、コンテキストメニューから「SonarLint: Bind to SonarQube/SonarCloud」を選択します。
3. 接続先のSonarQube/SonarCloudのURLや認証情報を入力します。
4. 表示されるプロジェクトのリストから、対象のプロジェクトを選択します。

#### 3.2 プロジェクトのバインド

- SonarLintがSonarQube/SonarCloudのプロジェクトにバインドされると、SonarLintはサーバー上のルールを使用してコードを解析します。

### 4. コード分析の実行

- コードファイルを開くと、SonarLintが自動的にコードを分析し、潜在的な問題点をリアルタイムで表示します。
- 問題のある行には警告マークが表示され、サイドバーから詳細情報を確認できます。

## 出力結果

- SonarLintによる分析結果は、VSCode内で直接確認できます。問題の箇所には警告マークが表示され、詳細情報はサイドバーから確認可能です。
- SonarQube/SonarCloudにバインドされたプロジェクトでは、全体的な品質レポートやトレンドをSonarQube/SonarCloudのダッシュボードで確認できます。

## 注意事項

- SonarLintは、リアルタイムでコードを分析するため、プロジェクトを開くたびに自動的に分析を実行します。
- SonarQube/SonarCloudとの連携により、より包括的な品質管理が可能となります。
