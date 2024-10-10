# フォルダ構成

## 目次
[[_TOC_]]

## フォルダ構成説明

このドキュメントでは、プロジェクトのフォルダ構成について説明します。

```
project-root/
│
├─ docs/                      # ドキュメント関連のフォルダ
│  ├─ 0-api-standardization/  # API開発プロセスの標準化に関するドキュメント
│  │     ├─ environment-standards.md  # 開発環境標準化
│  │     ├─ coding-standards.md        # コーディング規約作成
│  │     ├─ api-design-guidelines.md    # APIの設計基準作成
│  │     ├─ camunda-kafka-integration.md # Camunda、Kafka連携
│  │     ├─ documentation-rules.md      # ドキュメント化ルール
│  │     ├─ testing-automation.md        # テスト自動化
│  │     └─ ci-cd-implementation.md      # CI/CD導入
│  │
│  ├─ 1-architecture/         # アーキテクチャ関連のドキュメント
│  │     ├─ components.md     # コンポーネント詳細
│  │     └─ overview.md       # システム概要
│  │
│  ├─ 2-api/                  # API関連のドキュメント
│  │     ├─ api-design.md     # API設計
│  │     ├─ endpoints.md      # エンドポイント一覧
│  │     └─ examples.md       # リクエスト・レスポンス例
│  │
│  ├─ 3-database/             # データベース関連のドキュメント
│  │     ├─ schema.md         # データベーススキーマ
│  │     ├─ tables.md         # テーブル定義
│  │     └─ relationships.md   # テーブル間のリレーション
│  │
│  ├─ 4-testing/              # テスト関連のドキュメント
│  │     ├─ test-plan.md      # テスト計画
│  │     └─ test-cases.md     # テストケース一覧
│  │
│  └─ 5-user-manuals/        # ユーザーマニュアル
│        ├─ getting-started.md # はじめに
│        └─ user-guide.md      # ユーザーガイド
│
├─ README.md                  # プロジェクトの概要
└─ LICENSE                    # ライセンス情報
```

### フォルダの説明

#### docs/
プロジェクトのドキュメントが格納されているフォルダです。以下のサブフォルダが含まれます。

- **0-api-standardization/**  
  API開発プロセスの標準化に関するドキュメントが含まれています。具体的には、開発環境、コーディング規約、API設計基準、CamundaとKafkaの連携、ドキュメント化ルール、テスト自動化、CI/CD導入に関する情報が含まれています。

- **1-architecture/**  
  システムのアーキテクチャに関するドキュメントが含まれています。コンポーネント詳細とシステム概要に関するファイルがあります。

- **2-api/**  
  API関連のドキュメントが格納されています。API設計、エンドポイント一覧、リクエスト・レスポンスの例に関する情報があります。

- **3-database/**  
  データベース関連のドキュメントが含まれています。データベーススキーマ、テーブル定義、テーブル間のリレーションについてのファイルがあります。

- **4-testing/**  
  テストに関するドキュメントが含まれています。テスト計画とテストケース一覧が記載されています。

- **5-user-manuals/**  
  ユーザーマニュアルが格納されています。「はじめに」と「ユーザーガイド」に関する情報が含まれています。

### その他
- **README.md**  
  プロジェクトの概要を記載するファイルです。

- **LICENSE**  
  プロジェクトのライセンス情報を記載するファイルです。
