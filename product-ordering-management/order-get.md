# 注文取得API

## エンドポイント
/api/v1/order/{entrySheetNumber}

## リクエスト情報

| **＃** | **論理名**               | **物理名**       | **必須** | **型** | **桁数** |
| ------ | ------------------------ | ---------------- | -------- | ------ | -------- |
| 1      | 申込書番号               | entrySheetNumber | 必須     | String | 14       |
| 2      | ページ番号               | page             | 必須     | String | 4        |
| 3      | １ページ当たりの表示件数 | pageSize         | 必須     | String | 3        |

注文と注文明細が1:n
注文と本人確認情報が1:1
の関係。

本人確認書類登録ステータスには、
未登録、登録済/未審査、登録済/審査OK、登録済/審査NG
が入る。

## レスポンス情報

こちらが修正後の表です。番号の部分を連番にし、カテゴリに「メタ情報」を追加しました。

| **＃** | **分類**     | **論理名**                          | **物理名**                              | **説明** |
| ------ | ------------ | ----------------------------------- | --------------------------------------- | -------- |
| 1      | 注文         | 申込書番号                          | entrySheetNumber                        |          |
| 2      | 注文         | 契約タイプ                          | contractType                            |          |
| 3      | 注文         | SBB代理店コード                     | sbbAgencyCode                           |          |
| 4      | 注文         | SBM取次店コード                     | sbmShopCode                             |          |
| 5      | 注文         | 申込日                              | entryDate                               |          |
| 6      | 注文         | 契約者名のフリガナ（セイ）          | contractorNameSeiKana                   |          |
| 7      | 注文         | 契約者名のフリガナ（メイ）          | contractorNameMeiKana                   |          |
| 8      | 注文         | MySBのURL通知方法                   | mysbUrlNotificationMethod               |          |
| 9      | 注文         | 携帯電話番号                        | mobilePhoneNumber                       |          |
| 10     | 注文         | メールアドレス                      | mailAddress                             |          |
| 11     | 注文         | 注文ステータス                      | orderStatus                             |          |
| 12     | 本人確認情報 | 本人確認書類登録ステータス          | idDocRegistrationStatus                 |          |
| 13     | 本人確認情報 | 本確審査NG証明書種類                | idDocReviewNgCertType                   |          |
| 14     | 本人確認情報 | 本確審査NG証明書不備理由            | idDocReviewNgCertReason                 |          |
| 15     | 本人確認情報 | 本確審査NG証明書不備理由（予備）    | idDocReviewNgCertReasonAlt              |          |
| 16     | 本人確認情報 | 本確審査NG証明書種類2               | idDocReviewNgCertType2                  |          |
| 17     | 本人確認情報 | 本確審査NG証明書2不備理由           | idDocReviewNgCert2Reason                |          |
| 18     | 本人確認情報 | 本確審査NG証明書2不備理由（予備）   | idDocReviewNgCert2ReasonAlt             |          |
| 19     | 本人確認情報 | 本確審査NG追加証明書種類            | idDocReviewNgAdditionalCertType         |          |
| 20     | 本人確認情報 | 本確審査NG追加証明書不備理由        | idDocReviewNgAdditionalCertReason       |          |
| 21     | 本人確認情報 | 本確審査NG補助書類種類              | idDocReviewNgSupportDocType             |          |
| 22     | 本人確認情報 | 本確審査NG補助書類不備理由          | idDocReviewNgSupportDocReason           |          |
| 23     | 本人確認情報 | 本確審査NG補助書類不備理由（予備）  | idDocReviewNgSupportDocReasonAlt        |          |
| 24     | 本人確認情報 | 本確審査NG補助書類種類2             | idDocReviewNgSupportDocType2            |          |
| 25     | 本人確認情報 | 本確審査NG補助書類2不備理由         | idDocReviewNgSupportDoc2Reason          |          |
| 26     | 本人確認情報 | 本確審査NG補助書類2不備理由（予備） | idDocReviewNgSupportDoc2ReasonAlt       |          |
| 27     | 本人確認情報 | 本確審査NG追加補助書類種類          | idDocReviewNgAdditionalSupportDocType   |          |
| 28     | 本人確認情報 | 本確審査NG追加補助書類不備理由      | idDocReviewNgAdditionalSupportDocReason |          |
| 29     | 注文明細     | サービス                            | service                                 |          |
| 30     | 注文明細     | 業務                                | business                                |          |
| 31     | 注文明細     | 機器受取                            | equipmentReceipt                        |          |
| 32     | メタ情報     | page                                | 現在のページ番号                        |          |
| 33     | メタ情報     | pageSize                            | 1ページあたりの件数                     |          |
| 34     | メタ情報     | totalPages                          | 全ページ数                              |          |
| 35     | メタ情報     | totalItems                          | 全アイテム数                            |          |

## レスポンス情報の例

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
