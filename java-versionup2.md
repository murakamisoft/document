
### **1. 主な変更点とその影響**
#### **言語機能**
- **削除された機能**
  - `java.corba`や`java.xml.ws`など、一部のモジュールが削除されています。これらを使っている場合、修正が必要です。
- **文字列APIの変更**
  - `String`クラスに追加された新機能（例: `lines()`, `strip()`）が使えますが、古いAPIの挙動に依存している場合はテストが必要です。

#### **モジュールシステム**
- Java 9以降に導入されたモジュールシステム（JPMS）による影響を受ける可能性があります。従来のクラスパス方式のまま利用可能ですが、モジュールシステムの適用を検討する場合は大きな修正が必要です。

#### **ランタイムの変更**
- 内部API（`sun.misc`など）への依存がある場合は、リフレクションや代替APIへの移行が必要です。

#### **コーディング規約**
- Java 8以前の書き方が非推奨になり、警告やエラーが出るケースがあります（例: 原始型のボクシング／アンボクシングの不正確な使用）。

---

### **2. Spring Boot**
#### **Spring Bootのバージョン依存**
- Java 21に対応するには、Spring Bootのバージョンを **3.x** 以上にアップグレードする必要があります。
- この変更に伴い、次の作業が必要になります：
  - Springの設定ファイル（`application.properties`や`application.yml`）の更新。
  - `WebMvcConfigurer`など、非推奨または削除されたAPIの修正。

#### **依存ライブラリの更新**
- Spring Bootが依存しているライブラリ（例: Hibernate, Spring Dataなど）のバージョンも更新する必要があります。
- 特定の機能（例: セキュリティ設定）が削除またはリネームされている場合があります。

---

### **3. Gradle**
#### **Gradleバージョンアップ**
- Java 21を使用するにはGradleを **8.x** 以上に更新する必要があります。
- ビルドスクリプト（`build.gradle`）で非推奨の設定やプラグインを修正する必要がある場合があります。

---

### **修正量の見積もり**
以下は、一般的な中規模プロジェクト（20,000行程度）を想定した作業量の目安です：

| 修正作業                     | 修正量の目安          | コメント                                               |
| ---------------------------- | --------------------- | ------------------------------------------------------ |
| **Javaコード修正**           | 10〜30%のファイル修正 | 削除されたAPIや非推奨APIの使用箇所を修正する必要あり。 |
| **Spring Boot修正**          | 20〜40%のファイル修正 | バージョンアップによる設定やAPI変更の対応。            |
| **Gradle修正**               | 5〜10%のファイル修正  | プラグインや設定の更新。                               |
| **テストコード修正**         | 10〜20%のファイル修正 | 新しいランタイムやライブラリに対応するよう修正が必要。 |
| **モジュール化（必要なら）** | **オプション：+20%**  | モジュールシステムへの対応を行う場合の追加作業量。     |

---

### **推奨アプローチ**
1. **コードスキャン**:
   - 静的コード解析ツール（SonarQube, IntelliJのInspection）を使い、Java 8特有の書き方や非推奨APIを洗い出します。
2. **依存関係の整理**:
   - Gradleで依存しているライブラリを一覧化し、Java 21対応状況を確認します。
3. **段階的アップグレード**:
   - Java 8 → Java 11 → Java 17 → Java 21の順で段階的にアップグレードし、リスクを軽減します。
4. **テスト重視**:
   - 単体テスト、統合テストのカバレッジを高め、動作確認を徹底します。
