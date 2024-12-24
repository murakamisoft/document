以下に、追加された項目を「注文明細」分類として表にまとめました。

| **＃** | **分類** | **論理名**                          | **物理名**                              |
| ------ | -------- | ----------------------------------- | --------------------------------------- |
| 1      | 注文     | 契約タイプ                          | contractType                            |
| 2      | 注文     | SBB代理店コード                     | sbbAgencyCode                           |
| 3      | 注文     | SBM取次店コード                     | sbmShopCode                             |
| 4      | 注文     | 申込日                              | entryDate                               |
| 5      | 注文     | 契約者名のフリガナ（セイ）          | contractorNameSeiKana                   |
| 6      | 注文     | 契約者名のフリガナ（メイ）          | contractorNameMeiKana                   |
| 7      | 注文     | MySBのURL通知方法                   | mysbUrlNotificationMethod               |
| 8      | 注文     | 携帯電話番号                        | mobilePhoneNumber                       |
| 9      | 注文     | メールアドレス                      | mailAddress                             |
| 10     | 注文     | 注文ステータス                      | orderStatus                             |
| 11     | 注文     | 本人確認書類登録ステータス          | idDocRegistrationStatus                 |
| 12     | 注文     | 本確審査NG証明書種類                | idDocReviewNgCertType                   |
| 13     | 注文     | 本確審査NG証明書不備理由            | idDocReviewNgCertReason                 |
| 14     | 注文     | 本確審査NG証明書不備理由（予備）    | idDocReviewNgCertReasonAlt              |
| 15     | 注文     | 本確審査NG証明書種類2               | idDocReviewNgCertType2                  |
| 16     | 注文     | 本確審査NG証明書2不備理由           | idDocReviewNgCert2Reason                |
| 17     | 注文     | 本確審査NG証明書2不備理由（予備）   | idDocReviewNgCert2ReasonAlt             |
| 18     | 注文     | 本確審査NG追加証明書種類            | idDocReviewNgAdditionalCertType         |
| 19     | 注文     | 本確審査NG追加証明書不備理由        | idDocReviewNgAdditionalCertReason       |
| 20     | 注文     | 本確審査NG補助書類種類              | idDocReviewNgSupportDocType             |
| 21     | 注文     | 本確審査NG補助書類不備理由          | idDocReviewNgSupportDocReason           |
| 22     | 注文     | 本確審査NG補助書類不備理由（予備）  | idDocReviewNgSupportDocReasonAlt        |
| 23     | 注文     | 本確審査NG補助書類種類2             | idDocReviewNgSupportDocType2            |
| 24     | 注文     | 本確審査NG補助書類2不備理由         | idDocReviewNgSupportDoc2Reason          |
| 25     | 注文     | 本確審査NG補助書類2不備理由（予備） | idDocReviewNgSupportDoc2ReasonAlt       |
| 26     | 注文     | 本確審査NG追加補助書類種類          | idDocReviewNgAdditionalSupportDocType   |
| 27     | 注文     | 本確審査NG追加補助書類不備理由      | idDocReviewNgAdditionalSupportDocReason |
| 28     | 注文明細 | サービス                            | service                                 |
| 29     | 注文明細 | 業務                                | business                                |
| 30     | 注文明細 | 機器受取                            | equipmentReceipt                        |





注文と注文明細が1:nの関係である場合、レスポンスは注文情報と、その注文に関連する複数の注文明細を含む形でJSONを構造化できます。以下のサンプルJSONを参考にしてください：

```json
{
  "order": {
    "contractType": "契約タイプ例",
    "sbbAgencyCode": "SBB代理店コード例",
    "sbmShopCode": "SBM取次店コード例",
    "entryDate": "2024-12-24",
    "contractorNameSeiKana": "カントクタセイカナ",
    "contractorNameMeiKana": "カントクタメイカナ",
    "mysbUrlNotificationMethod": "通知方法例",
    "mobilePhoneNumber": "09012345678",
    "mailAddress": "example@mail.com",
    "orderStatus": "注文ステータス例",
    "idDocRegistrationStatus": "本人確認書類登録ステータス例",
    "idDocReviewNgCertType": "本確審査NG証明書種類例",
    "idDocReviewNgCertReason": "本確審査NG証明書不備理由例",
    "idDocReviewNgCertReasonAlt": "本確審査NG証明書不備理由（予備）例",
    "idDocReviewNgCertType2": "本確審査NG証明書種類2例",
    "idDocReviewNgCert2Reason": "本確審査NG証明書2不備理由例",
    "idDocReviewNgCert2ReasonAlt": "本確審査NG証明書2不備理由（予備）例",
    "idDocReviewNgAdditionalCertType": "本確審査NG追加証明書種類例",
    "idDocReviewNgAdditionalCertReason": "本確審査NG追加証明書不備理由例",
    "idDocReviewNgSupportDocType": "本確審査NG補助書類種類例",
    "idDocReviewNgSupportDocReason": "本確審査NG補助書類不備理由例",
    "idDocReviewNgSupportDocReasonAlt": "本確審査NG補助書類不備理由（予備）例",
    "idDocReviewNgSupportDocType2": "本確審査NG補助書類種類2例",
    "idDocReviewNgSupportDoc2Reason": "本確審査NG補助書類2不備理由例",
    "idDocReviewNgSupportDoc2ReasonAlt": "本確審査NG補助書類2不備理由（予備）例",
    "idDocReviewNgAdditionalSupportDocType": "本確審査NG追加補助書類種類例",
    "idDocReviewNgAdditionalSupportDocReason": "本確審査NG追加補助書類不備理由例"
  },
  "orderItems": [
    {
      "service": "サービス例",
      "business": "業務例",
      "equipmentReceipt": "機器受取例"
    },
    {
      "service": "サービス例2",
      "business": "業務例2",
      "equipmentReceipt": "機器受取例2"
    }
  ]
}
```

### 説明：
- **order**: 注文に関連する情報を含むオブジェクトです。注文の基本情報やステータス、本人確認書類の情報などが含まれます。
- **orderItems**: 注文明細の情報を含む配列です。1つの注文に対して複数の注文明細を保持できるように配列として扱っています。注文明細にはサービス、業務、機器受取に関する情報が格納されます。

これにより、1つの注文に対して複数の注文明細を関連づけて返すことができます。