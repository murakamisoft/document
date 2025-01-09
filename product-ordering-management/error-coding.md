以下は、エラーハンドリングの実装をMarkdownでまとめたものです。

---

# Spring Boot エラーハンドリング実装

## エラー形式
エラーレスポンスは以下の形式で返却されます：

```json
{
  "code": "422",
  "message": "Invalid request payload",
  "details": [
    {
      "field": "customer_id",
      "issue": "Customer ID is required"
    },
    {
      "field": "order_date",
      "issue": "Order date must be in the format YYYY-MM-DD"
    }
  ]
}
```

- `code`: HTTP ステータスコード（例: `422`）
- `message`: エラーの概要メッセージ
- `details`: エラーの詳細情報（フィールド名とその問題）

---

## 実装例

### 1. エラーレスポンス用のクラス

エラーレスポンスのクラス `ErrorResponse` とエラー詳細情報を持つ `ErrorDetail` クラスを定義します。

```java
import lombok.AllArgsConstructor;
import lombok.Getter;

import java.util.List;

@Getter
@AllArgsConstructor
public class ErrorResponse {
    private String code;
    private String message;
    private List<ErrorDetail> details;
}

@Getter
@AllArgsConstructor
public class ErrorDetail {
    private String field;
    private String issue;
}
```

- `ErrorResponse`: エラーレスポンスを表すクラスで、`code`、`message`、`details` を持ちます。
- `ErrorDetail`: 各エラーの詳細情報を格納するクラスで、`field` と `issue` を持ちます。

---

### 2. グローバルエラーハンドリングの設定

`@ControllerAdvice` を使い、全ての例外をキャッチしてエラーレスポンスを返却します。

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.context.request.WebRequest;

import java.util.Collections;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleAllExceptions(Exception ex, WebRequest request) {
        // 一般的なエラー処理
        ErrorResponse errorResponse = new ErrorResponse(
                HttpStatus.INTERNAL_SERVER_ERROR.name(),
                "Internal Server Error",
                Collections.singletonList(new ErrorDetail("unknown", ex.getMessage()))
        );
        return new ResponseEntity<>(errorResponse, HttpStatus.INTERNAL_SERVER_ERROR);
    }

    @ExceptionHandler(InvalidInputException.class)
    public ResponseEntity<ErrorResponse> handleInvalidInputException(InvalidInputException ex, WebRequest request) {
        // 入力エラーの処理
        ErrorDetail errorDetail = new ErrorDetail(ex.getField(), ex.getMessage());
        ErrorResponse errorResponse = new ErrorResponse(
                HttpStatus.UNPROCESSABLE_ENTITY.name(),  // 422 Unprocessable Entity
                "Invalid request payload",
                Collections.singletonList(errorDetail)
        );
        return new ResponseEntity<>(errorResponse, HttpStatus.UNPROCESSABLE_ENTITY);
    }
}
```

- `@ExceptionHandler`: 発生した例外を処理するメソッドに適用します。
- `HttpStatus.UNPROCESSABLE_ENTITY`: `422` ステータスコードを指定して、リクエストの内容が不正であることを伝えます。

---

### 3. カスタム例外クラス

リクエストの入力に問題がある場合、カスタム例外 `InvalidInputException` を使ってエラーを発生させます。

```java
import lombok.Getter;

@Getter
public class InvalidInputException extends RuntimeException {
    private String field;

    public InvalidInputException(String message, String field) {
        super(message);
        this.field = field;
    }
}
```

- `InvalidInputException`: 不正な入力に関連する例外で、エラーの詳細情報（`field`）も持っています。

---

### 4. コントローラーでの使用例

コントローラー内で `InvalidInputException` を発生させる例です。

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SampleController {

    @RequestMapping("/test")
    public String testMethod() {
        // バリデーションエラーを発生させる例
        throw new InvalidInputException("Customer ID is required", "customer_id");
    }
}
```

- `/test` エンドポイントにアクセスすると、`customer_id` フィールドにエラーが発生し、以下のエラーレスポンスが返されます。

---

### 5. 結果

エラー発生時、レスポンスは次のようになります。

```json
{
  "code": "422",
  "message": "Invalid request payload",
  "details": [
    {
      "field": "customer_id",
      "issue": "Customer ID is required"
    }
  ]
}
```

複数のエラー詳細を返す場合は、以下のようになります。

```json
{
  "code": "422",
  "message": "Invalid request payload",
  "details": [
    {
      "field": "customer_id",
      "issue": "Customer ID is required"
    },
    {
      "field": "order_date",
      "issue": "Order date must be in the format YYYY-MM-DD"
    }
  ]
}
```

---

## まとめ

- `422 Unprocessable Entity` を使用してリクエストが無効な場合にエラーを返す。
- `ErrorResponse` クラスでエラーレスポンスの構造を定義。
- グローバルエラーハンドリングで、`@ControllerAdvice` を使用して共通のエラー処理を行う。
- `InvalidInputException` で特定のエラーを投げ、適切なエラーメッセージをクライアントに返却。

