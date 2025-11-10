### 💡やりたいことの整理

DBには

```
0, 1, 2, 3
```

という **数値コード** が入っているけど、
画面には

```
定期収入, 臨時収入, 定期支出, 臨時支出
```

と表示したい。

---

### ✅ 実装例

#### ① Enumを作る

```java
public enum AllowanceType {
    REGULAR_INCOME(0, "定期収入"),
    TEMPORARY_INCOME(1, "臨時収入"),
    REGULAR_EXPENSE(2, "定期支出"),
    TEMPORARY_EXPENSE(3, "臨時支出");

    private final int code;
    private final String label;

    AllowanceType(int code, String label) {
        this.code = code;
        this.label = label;
    }

    public int getCode() {
        return code;
    }

    public String getLabel() {
        return label;
    }

    // code から Enum を取得する便利メソッド
    public static AllowanceType fromCode(int code) {
        for (AllowanceType type : values()) {
            if (type.code == code) {
                return type;
            }
        }
        return null; // または例外でもOK
    }
}
```

---

#### ② エンティティで Enum を使う

```java
@Entity
@Table(name = "t_allowance")
public class Allowance {

    @Id
    private Long id;

    private Integer type; // DB上はint

    @Transient // DBに保存しない
    private String typeName;

    // getterでEnumを利用
    public String getTypeName() {
        return AllowanceType.fromCode(this.type).getLabel();
    }

    // 省略...
}
```

---

#### ③ 一覧画面（DTOやレスポンス）で表示

もしAPIで返してReactなどに表示するなら、
DTOにマッピング時にEnumを展開します。

```java
public class AllowanceDto {
    private Long id;
    private Integer type;
    private String typeName;

    public static AllowanceDto fromEntity(Allowance entity) {
        AllowanceDto dto = new AllowanceDto();
        dto.id = entity.getId();
        dto.type = entity.getType();
        dto.typeName = AllowanceType.fromCode(entity.getType()).getLabel();
        return dto;
    }
}
```

---

### 🧩ポイント

* Enumに **codeと表示名(label)** を持たせる
* `fromCode()` でDB値からEnumへ変換
* 表示用には `getLabel()` を使う

---

もしJPAで直接Enumマッピング（`@Enumerated`）にしたい場合もできますが、
DBに「0,1,2,3」という数値をそのまま保存するなら上記の方が柔軟です。

