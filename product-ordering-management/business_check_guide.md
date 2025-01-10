# 業務チェックガイド

[[_TOC_]]

## 1. 概要
入力データが業務要件およびシステムルールに適合しているかを検証するための仕組みである。  
データの不整合や業務ルール違反を防ぎ、正確な業務遂行を保証することを目的とする。

---

## 2. チェックの目的
業務チェックの目的は、以下の通りである。

1. **データ整合性の維持**  
   エンティティ間のリレーションに基づき、整合性を検証すること。

2. **業務ルールの遵守**  
   システムの仕様に定められた業務ルールが遵守されていることを確認すること。

3. **不正入力の排除**  
   不正なデータや操作による業務プロセスの混乱を防止すること。

---

## 3. チェックの実施タイミング
業務チェックは以下のタイミングで実施される。

- **データ登録時**  
  注文エンティティおよび関連エンティティが業務ルールに適合しているかを確認する。

- **データ更新時**  
  エンティティの更新に伴い、関連データの整合性と業務ルールを再確認する。

---

## 4. 業務チェック項目
業務チェックの代表的な項目は、以下の通りである。

### 4.1 データ整合性チェック
1. **主キーおよび外部キーの整合性**  
   - 各エンティティの主キーが重複していないこと。
   - 外部キーが正しい関連先を参照していること。

2. **エンティティ間の関連チェック**  
   - 注文と注文明細が1:nの関係を満たしていること。
   - 注文明細に紐づく各種情報が存在すること。

### 4.2 業務ルールチェック
1. **必須項目の確認**  
   - カスタマID、プロダクトIDなど、必須項目が入力されていること。

2. **データ型およびフォーマットの確認**  
   - 数量は整数値であること。
   - 日付は正しいフォーマット（例: `YYYY-MM-DD`）であること。

3. **業務条件の検証**  
   - 例として、注文状態が「キャンセル」または「解約済み」の場合、新規の注文明細追加が行えないこと。（※業務仕様を要確認）
   - 例として、支払情報が複数登録されていないこと（例：銀行、ゆうちょ、クレジットカードはいずれか一つ）。（※業務仕様を要確認）

### 4.3 外部データの確認
1. **外部からのデータ検証**  
   - NTT関連情報（住所、提供事業者、照会情報など）が有効であること。（※業務仕様を要確認）
   - 申込yahoo情報が登録されている場合、そのフォーマットが適切であること。（※業務仕様を要確認）

2. **外部データの整合性**  
   - 注文に関連するNTT契約情報の各要素（住所、立会者など）が1:nの関係を満たしていること。

---

## 5. 業務チェックの実装

### 5.1 システム構成における実装
1. **API層**  
   Spring Bootを用いて、リクエストデータのバリデーションを行う。  
   Bean Validation（JSR 380）を活用し、入力データの形式や必須項目を検証する。

#### Bean Validation（JSR 380）の例

| **アノテーション** | **説明**                                                           | **例**                                                                                                             |
| ------------------ | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| `@NotNull`         | 値が`null`でないことを検証する。                                   | `@NotNull(message = "Customer ID is required") private Long customerId;`                                           |
| `@NotEmpty`        | コレクションや文字列が空でないことを検証する（`null`は無効）。     | `@NotEmpty(message = "Order items cannot be empty") private List<OrderItemRequest> items;`                         |
| `@Min`             | 数値が指定された最小値以上であることを検証する。                   | `@Min(value = 1, message = "Quantity must be at least 1") private int quantity;`                                   |
| `@Max`             | 数値が指定された最大値以下であることを検証する。                   | `@Max(value = 100, message = "Quantity must be less than or equal to 100") private int quantity;`                  |
| `@Size`            | コレクションや文字列の長さが指定された範囲内であることを検証する。 | `@Size(min = 1, max = 10, message = "Order items must be between 1 and 10") private List<OrderItemRequest> items;` |
| `@Pattern`         | 正規表現に一致する文字列であることを検証する。                     | `@Pattern(regexp = "\\d{4}-\\d{2}-\\d{2}", message = "Date must be in format YYYY-MM-DD") private String date;`    |
| `@Email`           | メールアドレス形式であることを検証する。                           | `@Email(message = "Email should be valid") private String email;`                                                  |
| `@Positive`        | 数値が正の値であることを検証する（0は含まない）。                  | `@Positive(message = "Price must be positive") private BigDecimal price;`                                          |
| `@PositiveOrZero`  | 数値が正または0であることを検証する。                              | `@PositiveOrZero(message = "Discount cannot be negative") private BigDecimal discount;`                            |
| `@Negative`        | 数値が負の値であることを検証する（0は含まない）。                  | `@Negative(message = "Temperature must be negative") private double temperature;`                                  |
| `@NegativeOrZero`  | 数値が負または0であることを検証する。                              | `@NegativeOrZero(message = "Balance cannot be positive") private BigDecimal balance;`                              |
| `@Future`          | 日付が現在より未来であることを検証する。                           | `@Future(message = "Delivery date must be in the future") private LocalDate deliveryDate;`                         |
| `@FutureOrPresent` | 日付が現在または未来であることを検証する。                         | `@FutureOrPresent(message = "Order date cannot be in the past") private LocalDate orderDate;`                      |
| `@Past`            | 日付が現在より過去であることを検証する。                           | `@Past(message = "Birthdate must be in the past") private LocalDate birthDate;`                                    |
| `@PastOrPresent`   | 日付が現在または過去であることを検証する。                         | `@PastOrPresent(message = "Registration date cannot be in the future") private LocalDate registrationDate;`        |

#### 実装例

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @PostMapping
    public ResponseEntity<OrderResponse> createOrder(@Valid @RequestBody OrderRequest orderRequest) {
        OrderResponse orderResponse = orderService.processOrder(orderRequest);
        return ResponseEntity.status(HttpStatus.CREATED).body(orderResponse);
    }
}
```

- `@Valid` アノテーションを使用して、入力データのバリデーションを実行する。
- バリデーション違反時にはエラーを返す。

**DTO例**:
```java
@Data
public class OrderRequest {
    @NotNull(message = "Customer ID is required")
    private Long customerId;

    @NotEmpty(message = "Order items cannot be empty")
    private List<OrderItemRequest> items;
}
```

**バリデーション例**:
```java
@Data
public class OrderItemRequest {
    @NotNull(message = "Product ID is required")
    private Long productId;

    @Min(value = 1, message = "Quantity must be at least 1")
    private int quantity;
}
```
