
## **✅ 確認事項一覧**
### **1. ネットワーク & プロキシ設定**
- [ ] **プロキシの影響でリポジトリアクセスが遅くなっていないか？**  
  - `gradle.properties` に設定したプロキシ情報が正しいか確認
  - 不要なプロキシ設定が入っていないか確認  

  ```properties
  systemProp.http.proxyHost=proxy.example.com
  systemProp.http.proxyPort=8080
  systemProp.http.proxyUser=username
  systemProp.http.proxyPassword=password
  ```

- [ ] **プロキシのログを確認**（リクエストがタイムアウトしていないか？）  
  - Gradleのログをデバッグレベルで確認  
  ```sh
  ./gradlew build --debug > gradle_debug.log
  ```

- [ ] **直接アクセスできるリポジトリを使えるか確認**  
  - プロキシ経由せず、直接リポジトリにアクセスできる環境があるか？  

---

### **2. アーティファクトURL & 認証設定**
- [ ] **`repositories` 設定が正しいか？**  
  - `build.gradle` または `settings.gradle` でURLが正しく設定されているか確認  
  ```kotlin
  repositories {
      maven {
          url "https://repo.example.com/maven2"
          credentials {
              username = project.findProperty("repoUsername") ?: ""
              password = project.findProperty("repoPassword") ?: ""
          }
      }
  }
  ```

- [ ] **認証情報が `gradle.properties` に正しく設定されているか？**
  ```properties
  repoUsername=myUser
  repoPassword=myPassword
  ```

- [ ] **認証失敗が発生していないか？**  
  - `gradle_debug.log` に `401 Unauthorized` や `403 Forbidden` が出ていないかチェック

---

### **3. キャッシュ & 依存関係の確認**
- [ ] **Gradleのキャッシュを有効化しているか？**  
  - キャッシュが有効になっているか確認（デフォルトは `true` だが念のためチェック）  
  ```properties
  org.gradle.caching=true
  ```

- [ ] **キャッシュが壊れていないか？**  
  - キャッシュが破損していると毎回ダウンロードし直すため、一度クリアしてみる  
  ```sh
  ./gradlew --stop
  ./gradlew clean
  rm -rf ~/.gradle/caches
  ```

- [ ] **不要な SNAPSHOT を使っていないか？**  
  - `SNAPSHOT` バージョンは毎回リモートチェックするため、`release` バージョンにできないか確認  

- [ ] **`gradle --refresh-dependencies` の影響を受けていないか？**  
  - このオプションを指定してビルドすると毎回リモートから取得するため、必要ないなら外す  

---

### **4. Gradleの並列ビルド & 設定最適化**
- [ ] **並列ビルドを有効化しているか？**  
  - 並列ビルドが有効なら `gradle.properties` に以下を設定  
  ```properties
  org.gradle.parallel=true
  ```

- [ ] **Gradleのデーモンが有効か？**  
  - デーモンを有効にするとビルド速度が向上する  
  ```properties
  org.gradle.daemon=true
  ```

- [ ] **`gradlew --no-daemon` を指定していないか？**  
  - `--no-daemon` を指定すると毎回プロセスが作られるため、速度低下の原因になる  

---

## **🛠 まとめ**
1. **ネットワーク & プロキシの影響をチェック**
2. **アーティファクトURLや認証設定を確認**
3. **Gradleのキャッシュや依存関係を最適化**
4. **Gradleの並列ビルドやデーモンを活用**
