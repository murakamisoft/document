# **Java 8からJava 21へのアップグレード：便利な新機能とコード例**

## **便利な新機能の一覧**

| **バージョン** | **新機能**                                                | **概要**                                               |
| -------------- | --------------------------------------------------------- | ------------------------------------------------------ |
| **Java 9**     | 不変コレクション (`List.of`, `Set.of`)                    | 不変のリストやセットを簡単に作成可能。                 |
| **Java 10**    | `var` による型推論                                        | 型を明示せずに変数を定義でき、コードが簡潔化。         |
| **Java 11**    | 文字列操作の新メソッド (`strip`, `lines`, `isBlank` など) | 文字列の前後の空白除去、行分割、空白判定などが簡単に。 |
| **Java 14**    | `switch` 式                                               | 複数ケースの条件分岐を簡潔に記述可能。                 |
| **Java 17**    | シールドクラス (`sealed`)                                 | 継承可能なクラスを明示的に制限。                       |
| **Java 19**    | 仮想スレッド（プレビュー機能）                            | 軽量スレッドで高い並列性を実現。                       |
| **Java 21**    | テンプレート文字列 (`StringTemplate`)                     | 文字列に変数を埋め込んで簡単に動的生成可能。           |

---

## **それぞれのコード例**

### **Java 9: 不変コレクション**
```java
import java.util.List;

public class Java9Example {
    public static void main(String[] args) {
        List<String> names = List.of("Alice", "Bob", "Charlie"); // 不変リスト （新たに要素の追加、削除、変更ができない）
        System.out.println(names);
    }
}
```

---

### **Java 10: 型推論（`var`）**
```java
import java.util.List;

public class Java10Example {
    public static void main(String[] args) {
        var names = List.of("Alice", "Bob", "Charlie"); // 型推論でList<String>  型が推論される。
        for (var name : names) {
            System.out.println(name);
        }
    }
}
```

---

### **Java 11: 文字列操作**
```java
public class Java11Example {
    public static void main(String[] args) {
        String multiline = " Hello \n World \n ";
        System.out.println("strip: [" + multiline.strip() + "]"); // 前後の空白を除去
        System.out.println("lines: " + multiline.lines().toList()); // 行ごとに分割
        System.out.println("isBlank: " + multiline.isBlank()); // 空白のみか確認
    }
}
```

---

### **Java 14: `switch` 式**
```java
public class Java14Example {
    public static void main(String[] args) {
        var day = "MONDAY";
        var type = switch (day) {
            case "SATURDAY", "SUNDAY" -> "Weekend";
            default -> "Weekday";
        };
        System.out.println("Day type: " + type);
    }
}
```

---

### **Java 17: シールドクラス**
```java
sealed interface Shape permits Circle, Square {}

final class Circle implements Shape {
    double radius;
    Circle(double radius) { this.radius = radius; }
}

final class Square implements Shape {
    double side;
    Square(double side) { this.side = side; }
}

public class Java17Example {
    public static void main(String[] args) {
        Shape shape = new Circle(5);
        if (shape instanceof Circle c) {
            System.out.println("Circle with radius: " + c.radius);
        }
    }
}
```

---

### **Java 19: 仮想スレッド**
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Java19Example {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor(); // 仮想スレッド
        for (int i = 0; i < 10; i++) {
            executor.submit(() -> {
                System.out.println("Hello from thread: " + Thread.currentThread());
            });
        }
        executor.shutdown();
    }
}
```

---

### **Java 21: テンプレート文字列**
```java
import java.lang.template.StringTemplate;

public class Java21Example {
    public static void main(String[] args) {
        int age = 41;
        String name = "John";
        String message = STR."Hello, \{name}. You are \{age} years old.";
        System.out.println(message);
    }
}
```
