以下は、注文登録APIのリクエストJSONの例です。指定通り、**注文ID**や**注文明細ID**などの採番は注文管理システム側で行うため、リクエストJSONには含めていません。

---

### 注文登録API リクエストJSON
```json
{
  "customerId": "CUSTOMER12345",
  "agentCodeId": "AGENT67890",
  "orderContacts": [
    {
      "name": "山田 太郎",
      "email": "taro.yamada@example.com",
      "phone": "09012345678"
    }
  ],
  "orderAddresses": [
    {
      "type": "billing",
      "postalCode": "123-4567",
      "addressLine1": "東京都港区芝浦1-1-1",
      "addressLine2": "マンション101号室"
    }
  ],
  "orderCustomers": [
    {
      "name": "佐藤 花子",
      "relationship": "配偶者",
      "age": 40
    }
  ],
  "orderReviews": [
    {
      "status": "承認待ち",
      "comments": "審査中"
    }
  ],
  "orderSohoCustomers": [
    {
      "department": "営業部",
      "contactPerson": {
        "name": "鈴木 次郎",
        "email": "jiro.suzuki@example.com",
        "phone": "03-1234-5678"
      },
      "subDepartments": [
        {
          "name": "東京営業課",
          "contactPerson": "田中 太郎"
        }
      ]
    }
  ],
  "orderDetails": [
    {
      "productId": "PROD001",
      "quantity": 2,
      "detailContacts": [
        {
          "name": "高橋 一郎",
          "email": "ichiro.takahashi@example.com",
          "phone": "07098765432"
        }
      ],
      "detailAddresses": [
        {
          "type": "shipping",
          "postalCode": "456-7890",
          "addressLine1": "大阪府大阪市北区梅田1-2-3",
          "addressLine2": "オフィス201"
        }
      ],
      "paymentInfo": {
        "method": "creditCard",
        "creditCard": {
          "cardNumber": "4111111111111111",
          "expirationDate": "12/26",
          "cardholderName": "山田 太郎"
        }
      },
      "yahooInfo": [
        {
          "username": "yamada_taro",
          "campaign": "キャンペーンA"
        }
      ],
      "nttContractInfo": [
        {
          "provider": "NTT東日本",
          "address": {
            "postalCode": "123-4567",
            "addressLine1": "東京都港区芝公園",
            "addressLine2": "2-1-1"
          },
          "schedule": {
            "constructionDate": "2025-02-01",
            "supervisor": "佐藤 次郎"
          }
        }
      ],
      "deviceManagement": [
        {
          "type": "ルーター",
          "model": "Model-X",
          "serialNumber": "SN12345678"
        }
      ]
    }
  ]
}
```

---

### JSON構造説明

1. **注文情報（Top-level）**
   - **`customerId`**: 顧客ID（外部キー）。
   - **`agentCodeId`**: 代理店コードID（外部キー）。

2. **注文系（`orderContacts`、`orderAddresses`、`orderCustomers`）**
   - **1:n関係**を反映し、リスト形式で表現。
   - 各要素に必要なフィールドを持たせています。

3. **審査情報（`orderReviews`）**
   - 状態やコメントを登録可能。

4. **注文_SOHO顧客情報（`orderSohoCustomers`）**
   - 主部門（`department`）とサブ部門（`subDepartments`）で1:nを表現。

5. **注文明細（`orderDetails`）**
   - **`productId`**と**`quantity`**を必須として記載。
   - **詳細連絡先（`detailContacts`）**、**詳細住所（`detailAddresses`）**など、注文明細に関連する情報をネスト。

6. **支払情報（`paymentInfo`）**
   - クレジットカードの例を記載していますが、他の支払方法（銀行、ゆうちょ）に対応する拡張が可能。

7. **申込関連（Yahoo、NTT、機器管理）**
   - 各情報を配列形式で表現（1:n関係を反映）。

---

### 留意点
- このJSONは柔軟性を持たせて設計していますが、実際の利用要件に応じて属性の追加・削除が可能です。
- 必須項目や入力制約は、設計時に明確化する必要があります。
- サンプルデータは例示であり、実際のデータ仕様に応じてカスタマイズしてください。

---

ご要望に応じて修正や詳細化を行いますので、フィードバックをお知らせください！