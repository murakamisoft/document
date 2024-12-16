
### 使用方法：
```javascript
var ss = SpreadsheetApp.openByUrl("https://docs.google.com/spreadsheets/d/1AbCdeFgHiJkLmNoPqRsTuvWxYz/edit");
```

### 完全なコード例：
もし検索値に基づいて別のシートを検索し、そのシート名を返したい場合、次のようにコードを記述します。

```javascript
function findSheetName(lookupValue) {
  // URLを指定してスプレッドシートを開く
  var ss = SpreadsheetApp.openByUrl("https://docs.google.com/spreadsheets/d/1AbCdeFgHiJkLmNoPqRsTuvWxYz/edit");
  
  var sheets = ss.getSheets();
  
  // 各シートをループして検索
  for (var i = 0; i < sheets.length; i++) {
    var sheet = sheets[i];
    var data = sheet.getDataRange().getValues(); // シート内の全データを取得
    
    // すべての行と列をループして検索
    for (var row = 0; row < data.length; row++) {
      for (var col = 0; col < data[row].length; col++) {
        if (data[row][col] == lookupValue) {
          return sheet.getName(); // ヒットしたシート名を返す
        }
      }
    }
  }
  
  return "見つかりません"; // 検索値が見つからなかった場合
}
```

### 説明：
- `SpreadsheetApp.openByUrl()`で、URLを指定してスプレッドシートを開きます。
- その後、`getSheets()`を使ってシートを取得し、`getDataRange().getValues()`で全データを取得します。
- 各シートを検索して、該当する値が見つかればそのシート名を返します。

### URLの形式：
指定するURLは、スプレッドシートの「編集」画面のURLで、`/edit`の部分まで含めて指定します（例：`https://docs.google.com/spreadsheets/d/{スプレッドシートID}/edit`）。