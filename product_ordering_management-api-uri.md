# ER図妄想。。。

```
【顧客】1 ──── N【契約】
         |
         └─── N【契約オプション】
         |
         └─── 1【プラン】

【契約】1 ──── N【契約履歴】

【契約】1 ──── 1【住所】

【契約】1 ──── 1【請求先情報】

【契約】1 ──── N【機器】

【契約オプション】N ──── 1【製品】
```

---

### **関係の説明**

1. **顧客と契約 (1:N)**  
   - 1人の顧客が複数の契約を持つことができる。

2. **契約と契約オプション (1:N)**  
   - 1つの契約に対して、複数のオプションが付けられる。

3. **契約とプラン (1:1)**  
   - 各契約は1つのプランに基づいている。

4. **契約と契約履歴 (1:N)**  
   - 各契約には複数の操作履歴（変更や更新など）が記録される。

5. **契約と住所 (1:1)**  
   - 契約には1つの住所（サービス提供先）が関連付けられる。

6. **契約と請求先情報 (1:1)**  
   - 契約には1つの請求先情報が関連付けられる。

7. **契約と機器 (1:N)**  
   - 1つの契約に対して、複数の関連機器が存在する可能性がある。

8. **契約オプションと製品 (N:1)**  
   - 各契約オプションは、特定の製品に紐付けられる。



# エンドポイント一覧

| No. | 操作               | HTTPメソッド | エンドポイント                                   | 説明                                 |
| --- | ------------------ | ------------ | ------------------------------------------------ | ------------------------------------ |
| 1   | 新規契約           | POST         | `/contracts`                                     | 新しい契約を作成する                 |
| 2   | オプション追加     | POST         | `/contracts/{contractId}/options`                | 契約にオプションを追加する           |
| 5   | 数量変更           | PATCH        | `/contracts/{contractId}/quantity`               | 契約内の数量を変更する               |
| 6   | 契約更新           | POST         | `/contracts/{contractId}/renewal`                | 契約期間を延長または更新する         |
| 7   | 一時停止           | POST         | `/contracts/{contractId}/pause`                  | 契約を一時停止する                   |
| 8   | 再開               | POST         | `/contracts/{contractId}/resume`                 | 一時停止中の契約を再開する           |
| 9   | 住所変更           | PUT          | `/contracts/{contractId}/address`                | 契約の住所を変更する                 |
| 10  | 名義変更           | PUT          | `/contracts/{contractId}/owner`                  | 契約者名義を変更する                 |
| 11  | 請求先情報変更     | PUT          | `/contracts/{contractId}/billing`                | 契約の請求先情報を変更する           |
| 12  | 機器交換           | POST         | `/contracts/{contractId}/equipment/swap`         | 契約内の機器を交換する               |
| 13  | 解約               | DELETE       | `/customers/{customerId}/contracts/{contractId}` | 契約を解約する                       |
| 14  | 契約一覧取得       | GET          | `/contracts`                                     | 全ての契約情報を取得する             |
| 15  | 契約詳細取得       | GET          | `/contracts/{contractId}`                        | 指定された契約の詳細情報を取得する   |
| 16  | オプション一覧取得 | GET          | `/contracts/{contractId}/options`                | 契約に紐づくオプション一覧を取得する |
| 17  | 顧客情報取得       | GET          | `/customers/{customerId}`                        | 指定された顧客の情報を取得する       |
| 18  | 契約履歴取得       | GET          | `/contracts/{contractId}/history`                | 指定された契約の変更履歴を取得する   |




## 1. 新規契約 (`POST /contracts`)

**リクエスト**:
```json
{
  "customerId": "12345",
  "startDate": "2024-12-01",
  "products": [
    {
      "productId": "P001",
      "quantity": 2
    }
  ]
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "status": "active",
  "startDate": "2024-12-01",
  "products": [
    {
      "productId": "P001",
      "quantity": 2
    }
  ]
}
```

---

## 2. オプション追加 (`POST /contracts/{contractId}/options`)

**リクエスト**:
```json
{
  "productId": "P002",
  "quantity": 1
}
```

**レスポンス**:
```json
{
  "optionId": "45678",
  "productId": "P002",
  "quantity": 1,
  "status": "追加完了"
}
```

---

## 3. オプション削除 (`DELETE /contracts/{contractId}/options/{optionId}`)

**リクエスト**:
なし

**レスポンス**:
```json
{
  "optionId": "45678",
  "status": "削除完了"
}
```

---

## 4. プラン変更 (`PUT /contracts/{contractId}/plan`)

**リクエスト**:
```json
{
  "newPlanId": "premium"
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "oldPlanId": "basic",
  "newPlanId": "premium",
  "status": "変更完了"
}
```

---

## 5. 数量変更 (`PATCH /contracts/{contractId}/quantity`)

**リクエスト**:
```json
{
  "productId": "P001",
  "newQuantity": 3
}
```

**レスポンス**:
```json
{
  "productId": "P001",
  "oldQuantity": 2,
  "newQuantity": 3,
  "status": "変更完了"
}
```

---

## 6. 契約更新 (`POST /contracts/{contractId}/renewal`)

**リクエスト**:
```json
{
  "newEndDate": "2025-12-01"
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "newEndDate": "2025-12-01",
  "status": "更新完了"
}
```

---

## 7. 一時停止 (`POST /contracts/{contractId}/pause`)

**リクエスト**:
```json
{
  "reason": "停止理由"
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "status": "一時停止",
  "reason": "停止理由"
}
```

---

## 8. 再開 (`POST /contracts/{contractId}/resume`)

**リクエスト**:
なし

**レスポンス**:
```json
{
  "contractId": "98765",
  "status": "アクティブ"
}
```

---

## 9. 住所変更 (`PUT /contracts/{contractId}/address`)

**リクエスト**:
```json
{
  "newAddress": "東京都渋谷区神南1丁目1-1"
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "oldAddress": "大阪府大阪市北区梅田1丁目2-2",
  "newAddress": "東京都渋谷区神南1丁目1-1",
  "status": "変更完了"
}
```

---

## 10. 名義変更 (`PUT /contracts/{contractId}/owner`)

**リクエスト**:
```json
{
  "newOwnerName": "山田 太郎"
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "oldOwnerName": "佐藤 花子",
  "newOwnerName": "山田 太郎",
  "status": "変更完了"
}
```

---

## 11. 請求先情報変更 (`PUT /contracts/{contractId}/billing`)

**リクエスト**:
```json
{
  "newBillingInfo": {
    "address": "神奈川県横浜市中区山下町2丁目3-3",
    "paymentMethod": "クレジットカード"
  }
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "status": "変更完了",
  "billingInfo": {
    "address": "神奈川県横浜市中区山下町2丁目3-3",
    "paymentMethod": "クレジットカード"
  }
}
```

---

## 12. 機器交換 (`POST /contracts/{contractId}/equipment/swap`)

**リクエスト**:
```json
{
  "oldEquipmentId": "001",
  "newEquipmentId": "002"
}
```

**レスポンス**:
```json
{
  "contractId": "98765",
  "oldEquipmentId": "001",
  "newEquipmentId": "002",
  "status": "交換完了"
}
```

---

## 13. 解約 (`DELETE /customers/{customerId}/contracts/{contractId}`)

**リクエスト**:
なし

**レスポンス**:
```json
{
  "contractId": "98765",
  "status": "解約完了",
  "terminationDate": "2024-12-06"
}
```

---

## 14. 契約一覧取得 (`GET /contracts`)

**リクエスト**:
なし

**レスポンス**:
```json
[
  {
    "contractId": "98765",
    "status": "アクティブ",
    "startDate": "2024-12-01"
  },
  {
    "contractId": "87654",
    "status": "一時停止",
    "startDate": "2024-10-01"
  }
]
```

---

## 15. 契約詳細取得 (`GET /contracts/{contractId}`)

**リクエスト**:
なし

**レスポンス**:
```json
{
  "contractId": "98765",
  "status": "アクティブ",
  "startDate": "2024-12-01",
  "products": [
    {
      "productId": "P001",
      "quantity": 2
    }
  ]
}
```

---

## 16. オプション一覧取得 (`GET /contracts/{contractId}/options`)

**リクエスト**:
なし

**レスポンス**:
```json
[
  {
    "optionId": "45678",
    "productId": "P002",
    "quantity": 1
  }
]
```

---

## 17. 顧客情報取得 (`GET /customers/{customerId}`)

**リクエスト**:
なし

**レスポンス**:
```json
{
  "customerId": "12345",
  "name": "田中 一郎",
  "email": "tanaka.ichiro@example.jp",
  "phone": "090-1234-5678"
}
```

---

## 18. 契約履歴取得 (`GET /contracts/{contractId}/history`)

**リクエスト**:
なし

**レスポンス**:
```json
[
  {
    "action": "作成",
    "timestamp": "2024-12-01T12:00:00Z",
    "details": "SB光ベーシックプランで契約を作成"
  },
  {
    "action": "更新",
    "timestamp": "2024-12-05T08:30:00Z",
    "details": "SB光ベーシックプランを速度上位プランに変更"
  }
]
```
