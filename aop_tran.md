もちろん！クラスレベルの `@Transactional` にも対応した実装をもう一度まとめてお伝えするね。

---

## **💡 クラスレベルの `@Transactional` に対応した AOP 実装**

### **1️⃣ AOP クラスの実装**
```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.transaction.annotation.Transactional;

@Aspect
@Component
public class TransactionLoggingAspect {

    private static final Logger logger = LoggerFactory.getLogger(TransactionLoggingAspect.class);

    // @Transactionalがついたメソッドまたはクラスに対応
    @Around("@annotation(transactional) || within(@org.springframework.transaction.annotation.Transactional *)")
    public Object logTransaction(ProceedingJoinPoint joinPoint, Transactional transactional) throws Throwable {
        String methodName = joinPoint.getSignature().toShortString();
        
        // トランザクション開始前
        logger.info("【トランザクション開始】メソッド: {}", methodName);

        try {
            Object result = joinPoint.proceed(); // メソッド実行（Springのトランザクション処理がここで動く）
            
            // トランザクション正常終了
            logger.info("【トランザクション正常終了】メソッド: {}", methodName);
            return result;
        } catch (Exception e) {
            // トランザクション異常終了
            logger.error("【トランザクション異常終了】メソッド: {}, エラー: {}", methodName, e.getMessage(), e);
            throw e;
        }
    }
}
```

### **2️⃣ 使用するサービスクラスの例**
```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Transactional // クラスレベルでトランザクションを適用
@Service
public class OrderService {

    public void processOrder() {
        System.out.println("注文処理中...");
        // ここでトランザクションが開始される
    }

    public void cancelOrder() {
        System.out.println("注文キャンセル中...");
        // ここでトランザクションが開始される
    }
}
```

---

## **💡 ポイント**

- **`@Around("@annotation(transactional) || within(@org.springframework.transaction.annotation.Transactional *)")`**  
  この AOP 設定により、`@Transactional` がメソッドに付いている場合も、クラスに付いている場合も対応可能！
  
- **メソッド実行前・後にログを出力**  
  トランザクションの開始、正常終了、異常終了のタイミングでログが出力されるので、どのメソッドがどのタイミングで実行されているかを追いやすくなる。

---

## **💡 実行結果**

### **正常なトランザクションのログ**
```plaintext
INFO  【トランザクション開始】メソッド: OrderService.processOrder()
注文処理中...
INFO  【トランザクション正常終了】メソッド: OrderService.processOrder()

INFO  【トランザクション開始】メソッド: OrderService.cancelOrder()
注文キャンセル中...
INFO  【トランザクション正常終了】メソッド: OrderService.cancelOrder()
```

### **例外発生時のロールバックのログ**
```java
public void processOrder() {
    System.out.println("注文処理中...");
    throw new RuntimeException("エラー発生！");
}
```
```plaintext
INFO  【トランザクション開始】メソッド: OrderService.processOrder()
注文処理中...
ERROR 【トランザクション異常終了】メソッド: OrderService.processOrder(), エラー: エラー発生！
```

---

## **まとめ**
- **クラスレベルの `@Transactional` にも対応** できるように、AOP の設定に `within(@org.springframework.transaction.annotation.Transactional *)` を追加しました。
- **トランザクションの前後でログが自動で出力** されるので、トランザクションの動作確認が簡単に行えます。
  
この実装をそのまま使って、トランザクションログの管理を行ってください！  
また何か質問があれば、気軽に聞いてね😊