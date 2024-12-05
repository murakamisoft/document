## **1. Docker Desktopの確認**
1. **Docker Desktopが起動しているか確認**
   - Docker Desktopを開き、ダッシュボードで`Engine running`などが表示されていればOK。

2. **Docker Composeの確認**
   ```bash
   docker compose version
   ```
   - 正常に動作しているか確認してください。

---

## **2. プロジェクトの準備**
### **Spring Bootプロジェクトを作成**
1. **Spring Initializrを使用**
   - [Spring Initializr](https://start.spring.io/)を開き、以下の設定でプロジェクトを作成：
     - Project: Gradle - Groovy
     - Language: Java
     - Spring Boot: 最新バージョン
     - Dependencies:
       - Spring Web
       - Spring Data JPA
       - PostgreSQL Driver

2. プロジェクトをダウンロードし、ローカルに解凍します。

---

## **3. `Dockerfile`の作成**
Spring BootアプリケーションのDockerイメージを作成するために、プロジェクトのルートディレクトリに`Dockerfile`を作成します。

**`Dockerfile`**
```Dockerfile
# 基本イメージ
FROM openjdk:21-jdk-slim

# 作業ディレクトリを設定
WORKDIR /app

# Gradleキャッシュを活用するための依存ファイルコピー
COPY build.gradle.kts settings.gradle.kts ./
COPY src ./src

# Gradleを使用してプロジェクトをビルド
RUN apt-get update && apt-get install -y curl && \
    curl -s "https://get.sdkman.io" | bash && \
    bash -c "source /root/.sdkman/bin/sdkman-init.sh && sdk install gradle && gradle build"

# JARファイルを実行
ENTRYPOINT ["java", "-jar", "build/libs/*.jar"]
```

---

## **4. `docker-compose.yml`の作成**
PostgreSQLとSpring Bootアプリケーションを一緒に管理するために、`docker-compose.yml`を作成します。

**`docker-compose.yml`**
```yaml
version: '3.8'

services:
  db:
    image: postgres:latest
    container_name: postgres_container
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: springboot_app
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/mydatabase
      SPRING_DATASOURCE_USERNAME: myuser
      SPRING_DATASOURCE_PASSWORD: mypassword
      SPRING_JPA_HIBERNATE_DDL_AUTO: update
    depends_on:
      - db

volumes:
  postgres_data:
```

---

## **5. Dockerでアプリケーションをビルド・起動**
1. **Dockerイメージのビルド**
   ```bash
   docker compose build
   ```

2. **Dockerコンテナの起動**
   ```bash
   docker compose up
   ```

3. **動作確認**
   - ブラウザで`http://localhost:8080`にアクセスしてSpring Bootアプリケーションが起動していることを確認します。

---

## **6. 必要に応じての確認**
1. **PostgreSQLに接続**
   - `pgAdmin`や`psql`を使用して、PostgreSQLに接続してデータベースが正しくセットアップされているか確認します。

2. **ログの確認**
   - コンテナログを確認して、問題がないかチェック：
     ```bash
     docker compose logs
     ```
