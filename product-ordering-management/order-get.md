# 注文取得API

レスポンスのJSONを教えて。
No12～27は、運転免許証、健康保険証＋補助書類、日本国パスポート＋補助書類、公共料金領収書、有効期限が切れています、画像が切れているため確認できませんでした。
を使用して、値を考えて。

注文と注文明細が1:n
注文と本人確認情報が1:1
の関係。

| **＃** | **分類**     | **論理名**                          | **物理名**                              |
| ------ | ------------ | ----------------------------------- | --------------------------------------- |
| 1      | 注文         | 契約タイプ                          | contractType                            |
| 2      | 注文         | SBB代理店コード                     | sbbAgencyCode                           |
| 3      | 注文         | SBM取次店コード                     | sbmShopCode                             |
| 4      | 注文         | 申込日                              | entryDate                               |
| 5      | 注文         | 契約者名のフリガナ（セイ）          | contractorNameSeiKana                   |
| 6      | 注文         | 契約者名のフリガナ（メイ）          | contractorNameMeiKana                   |
| 7      | 注文         | MySBのURL通知方法                   | mysbUrlNotificationMethod               |
| 8      | 注文         | 携帯電話番号                        | mobilePhoneNumber                       |
| 9      | 注文         | メールアドレス                      | mailAddress                             |
| 10     | 注文         | 注文ステータス                      | orderStatus                             |
| 11     | 本人確認情報 | 本人確認書類登録ステータス          | idDocRegistrationStatus                 | 未登録、登録済/未審査、登録済/審査OK、登録済/審査NG |
| 12     | 本人確認情報 | 本確審査NG証明書種類                | idDocReviewNgCertType                   |
| 13     | 本人確認情報 | 本確審査NG証明書不備理由            | idDocReviewNgCertReason                 |
| 14     | 本人確認情報 | 本確審査NG証明書不備理由（予備）    | idDocReviewNgCertReasonAlt              |
| 15     | 本人確認情報 | 本確審査NG証明書種類2               | idDocReviewNgCertType2                  |
| 16     | 本人確認情報 | 本確審査NG証明書2不備理由           | idDocReviewNgCert2Reason                |
| 17     | 本人確認情報 | 本確審査NG証明書2不備理由（予備）   | idDocReviewNgCert2ReasonAlt             |
| 18     | 本人確認情報 | 本確審査NG追加証明書種類            | idDocReviewNgAdditionalCertType         |
| 19     | 本人確認情報 | 本確審査NG追加証明書不備理由        | idDocReviewNgAdditionalCertReason       |
| 20     | 本人確認情報 | 本確審査NG補助書類種類              | idDocReviewNgSupportDocType             |
| 21     | 本人確認情報 | 本確審査NG補助書類不備理由          | idDocReviewNgSupportDocReason           |
| 22     | 本人確認情報 | 本確審査NG補助書類不備理由（予備）  | idDocReviewNgSupportDocReasonAlt        |
| 23     | 本人確認情報 | 本確審査NG補助書類種類2             | idDocReviewNgSupportDocType2            |
| 24     | 本人確認情報 | 本確審査NG補助書類2不備理由         | idDocReviewNgSupportDoc2Reason          |
| 25     | 本人確認情報 | 本確審査NG補助書類2不備理由（予備） | idDocReviewNgSupportDoc2ReasonAlt       |
| 26     | 本人確認情報 | 本確審査NG追加補助書類種類          | idDocReviewNgAdditionalSupportDocType   |
| 27     | 本人確認情報 | 本確審査NG追加補助書類不備理由      | idDocReviewNgAdditionalSupportDocReason |
| 28     | 注文明細     | サービス                            | service                                 |
| 29     | 注文明細     | 業務                                | business                                |
| 30     | 注文明細     | 機器受取                            | equipmentReceipt                        |



```json
{
  "order": {
    "contractType": "新規契約",
    "sbbAgencyCode": "AG123456",
    "sbmShopCode": "SHOP98765",
    "entryDate": "2024-12-24",
    "contractorNameSeiKana": "ヤマダ",
    "contractorNameMeiKana": "タロウ",
    "mysbUrlNotificationMethod": "メール",
    "mobilePhoneNumber": "09012345678",
    "mailAddress": "example@example.com",
    "orderStatus": "受付中",
    "idDocInfo": {
      "idDocRegistrationStatus": "登録済/審査NG",
      "idDocReviewNgCertType": "運転免許証",
      "idDocReviewNgCertReason": "有効期限が切れています",
      "idDocReviewNgCertReasonAlt": "画像が切れているため確認できませんでした",
      "idDocReviewNgCertType2": "健康保険証＋補助書類",
      "idDocReviewNgCert2Reason": "有効期限が切れています",
      "idDocReviewNgCert2ReasonAlt": "画像が切れているため確認できませんでした",
      "idDocReviewNgAdditionalCertType": "日本国パスポート＋補助書類",
      "idDocReviewNgAdditionalCertReason": "画像が切れているため確認できませんでした",
      "idDocReviewNgSupportDocType": "公共料金領収書",
      "idDocReviewNgSupportDocReason": "有効期限が切れています",
      "idDocReviewNgSupportDocReasonAlt": "画像が切れているため確認できませんでした",
      "idDocReviewNgSupportDocType2": "運転免許証",
      "idDocReviewNgSupportDoc2Reason": "有効期限が切れています",
      "idDocReviewNgSupportDoc2ReasonAlt": "画像が切れているため確認できませんでした",
      "idDocReviewNgAdditionalSupportDocType": "健康保険証＋補助書類",
      "idDocReviewNgAdditionalSupportDocReason": "画像が切れているため確認できませんでした"
    },
    "orderItems": [
      {
        "service": "インターネット",
        "business": "法人向け",
        "equipmentReceipt": "店舗受取"
      },
      {
        "service": "固定電話",
        "business": "個人向け",
        "equipmentReceipt": "宅配受取"
      }
    ]
  }
}

```
