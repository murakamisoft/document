# 取得APIで0件の場合のHTTPレスポンスステータスについて

## 概要

APIレスポンスにおける**一覧取得（検索系）API**と**単一リソース取得API**のステータスコードやレスポンスの取り扱いについてまとめます。

## 目次

1. 一覧取得（検索系）API
2. 単一リソース取得API
3. メタ情報の設計

---

## 1. 一覧取得（検索系）API

### レスポンスコード

- **200 OK**
  - 検索条件に一致するアイテムが0件だった場合、`200 OK`ステータスコードを返します。
  - 検索は正常に処理され、結果が0件であることが明示されます。

### レスポンス構造

レスポンスには以下の情報が含まれます：
- **data**: 検索結果として一致したアイテム（0件の場合、空の配列）。
- **metaInformation**: メタ情報。
  - **page**: 現在のページ番号（1ページ目は1）。
  - **pageSize**: 1ページあたりのアイテム数。
  - **totalPages**: 全ページ数。
  - **totalItems**: 全アイテム数。

### 例

#### 検索結果が0件の場合：

```json
{
  "data": [],
  "metaInformation": {
    "page": 1,
    "pageSize": 10,
    "totalPages": 0,
    "totalItems": 0
  }
}
```

---

## 2. 単一リソース取得API

### レスポンスコード

- **404 Not Found**
  - リソースを取得する際に、指定されたID（または主キー）に対応するデータが存在しない場合、`404 Not Found`ステータスコードを返します。
  - リクエストが正しく送られたが、リソースが見つからなかった場合。
