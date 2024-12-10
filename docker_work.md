React（フロントエンド）、
Spring Boot（バックエンド、MyBatis使用）、
PostgreSQLを統合した開発環境をDockerで構築

---

## 1. **プロジェクトのディレクトリ構成**

以下のディレクトリ構造を用意します：

```
project-root/
├── backend/          # Spring Bootプロジェクト
├── frontend/         # Reactプロジェクト
├── db/               # PostgreSQL初期化用ファイル
├── docker-compose.yml
```

---

## 2. **React（フロントエンド）の構築**

1. **Reactプロジェクトの作成**  
   ```bash
   cd frontend
   npx create-react-app .
   ```

2. **Dockerfileを作成**  
   `frontend/Dockerfile`:
   ```dockerfile
   FROM node:18

   # 作業ディレクトリ
   WORKDIR /app

   # パッケージインストール
   COPY package.json yarn.lock ./
   RUN yarn install

   # ソースコードをコピー
   COPY . .

   # Reactアプリの起動
   CMD ["yarn", "start"]
   ```

3. **.dockerignoreを作成**  
   `frontend/.dockerignore`:
   ```
   node_modules
   build
   ```

---

## 3. **Spring Boot（バックエンド）の構築**

### A. Spring Bootプロジェクトを作成

Spring Initializr（[公式サイト](https://start.spring.io/)）を使い、以下の設定でプロジェクトを生成します：

- **Dependencies**: Spring Web, MyBatis Framework, PostgreSQL Driver  
- **Java Version**: 21  
- **Build Tool**: Gradle

**生成されたプロジェクトを`backend/`に配置**してください。

---

### B. `build.gradle`の編集

以下の依存関係を`backend/build.gradle`に追加します：

```gradle
dependencies {
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.2'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.postgresql:postgresql'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

---

### C. `application.yml`の設定

`backend/src/main/resources/application.yml`に以下を記述します：

```yaml
spring:
  datasource:
    url: jdbc:postgresql://db:5432/mydb
    username: postgres
    password: postgres
  mybatis:
    mapper-locations: classpath:mapper/*.xml
    type-aliases-package: com.example.domain
```

---

### D. エンティティ作成

例として、`User`エンティティを作成します。  
`backend/src/main/java/com/example/domain/User.java`:

```java
package com.example.domain;

public class User {
    private Long id;
    private String name;
    private String email;

    // Getter & Setter
    public Long getId() {
        return id;
    }
    public void setId(Long id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getEmail() {
        return email;
    }
    public void setEmail(String email) {
        this.email = email;
    }
}
```

---

### E. Mapperインターフェース作成

`backend/src/main/java/com/example/mapper/UserMapper.java`:

```java
package com.example.mapper;

import com.example.domain.User;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Mapper
public interface UserMapper {
    List<User> findAll();
    void insert(User user);
    User findById(Long id);
}
```

---

### F. Mapper XMLの作成

`backend/src/main/resources/mapper/UserMapper.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.mapper.UserMapper">

    <resultMap id="UserResultMap" type="com.example.domain.User">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="email" column="email"/>
    </resultMap>

    <select id="findAll" resultMap="UserResultMap">
        SELECT * FROM users
    </select>

    <select id="findById" parameterType="Long" resultMap="UserResultMap">
        SELECT * FROM users WHERE id = #{id}
    </select>

    <insert id="insert" parameterType="com.example.domain.User">
        INSERT INTO users (name, email) VALUES (#{name}, #{email})
    </insert>

</mapper>
```

---

### G. Dockerfileを作成

`backend/Dockerfile`:

```dockerfile
FROM eclipse-temurin:21-jdk-alpine

# 作業ディレクトリ
WORKDIR /app

# ソースコードをコピー
COPY build/libs/*.jar app.jar

# アプリケーションの起動
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 4. **PostgreSQLの設定**

1. `db/init.sql`に初期化スクリプトを作成します：
   ```sql
   CREATE DATABASE mydb;
   CREATE USER postgres WITH ENCRYPTED PASSWORD 'postgres';
   GRANT ALL PRIVILEGES ON DATABASE mydb TO postgres;

   CREATE TABLE users (
       id SERIAL PRIMARY KEY,
       name VARCHAR(100),
       email VARCHAR(100)
   );
   ```

---

## 5. **Docker Composeの設定**

`docker-compose.yml`をプロジェクトルートに作成します：

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
    stdin_open: true
    tty: true

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    depends_on:
      - db
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/mydb
      SPRING_DATASOURCE_USERNAME: postgres
      SPRING_DATASOURCE_PASSWORD: postgres

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - ./db:/docker-entrypoint-initdb.d
    ports:
      - "5432:5432"
```

---

## 6. **アプリケーションの起動**

1. Docker Composeでサービスをビルド＆起動します：
   ```bash
   docker-compose up --build
   ```

2. フロントエンド: [http://localhost:3000](http://localhost:3000)  
3. バックエンド: [http://localhost:8080](http://localhost:8080)
