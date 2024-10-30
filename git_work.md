## Gitでコントローラクラス一覧を更新日時の降順で取得する方法

Spring Bootのプロジェクトにおいて、`Controller`で終わるクラスを対象に、更新日時の降順で一覧を取得するために、以下のコマンドを使用します。

### コマンド

```bash
git log --name-only --pretty=format: --diff-filter=A -- '*.java' | grep 'Controller' | uniq | xargs -I {} git log -1 --format="%ai %an %h {}" {} | sort -r
```

### コマンドの各部分の説明

- **`git log --name-only --pretty=format: --diff-filter=A -- '*.java'`**  
  Javaファイルのみに絞り、ファイル名のみの変更履歴を取得します。
  
- **`grep 'Controller'`**  
  クラス名が「Controller」で終わるファイルを抽出します。

- **`uniq`**  
  重複するファイル名を削除し、一意にします。

- **`xargs -I {} git log -1 --format="%ai %an %h {}" {}`**  
  各ファイルについて最新の更新日時、更新者、コミットハッシュ、ファイルパスを取得します。

- **`sort -r`**  
  更新日時の降順でソートします。

### 出力例

このコマンドを実行すると、以下のような出力が得られます。

```plaintext
2024-10-28 10:15:32 +0900 John Doe a1b2c3d4 src/main/java/com/example/controller/SampleController.java
2024-10-26 15:42:11 +0900 Jane Smith e5f6g7h8 src/main/java/com/example/controller/AnotherController.java
...
