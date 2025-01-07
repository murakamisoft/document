以下はリクエストボディのJSON例です。

### **注文ステータス更新APIリクエストボディ例**

#### **例1: 注文ステータスを「CONFIRMED」に更新する場合**
```json
{
  "orderId": "ORD12345678",
  "status": "CONFIRMED",
  "updatedBy": "USER_001"
}
```

#### **例2: 注文ステータスを「CANCELED」に更新する場合**
```json
{
  "orderId": "ORD98765432",
  "status": "CANCELED",
  "updatedBy": "ADMIN_002"
}
```

#### **例3: ステータス更新者がシステムの場合**
```json
{
  "orderId": "ORD55555555",
  "status": "SHIPPED",
  "updatedBy": "SYSTEM"
}
```

#### **例4: 任意項目 `updatedBy` を省略した場合**
（更新者情報が不要な場合でも、最低限 `orderId` と `status` は必要）
```json
{
  "orderId": "ORD12345678",
  "status": "APPROVED"
}
```

---

### **補足**
- **`orderId`:**  
  各注文を一意に識別するための注文IDです。形式はシステム設計によって異なりますが、英数字を組み合わせた形式を使用することが一般的です。

- **`status`:**  
  許容されるステータス値のみを指定してください（例: `PENDING`, `CONFIRMED`, `CANCELED`, など）。値は事前定義されたリストでバリデーションを行います。

- **`updatedBy`:**  
  誰がこのステータスを更新したかを記録するための情報です。ユーザー名やシステム名など、識別可能な値を設定します。

必要に応じてこのJSON例をベースに仕様書やテストケースを作成してください！