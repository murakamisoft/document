mockMvc.perform(post("/orders")
        .contentType(MediaType.APPLICATION_JSON)
        .content("{\"orderId\":\"123\", \"orderDescription\":\"Sample order\"}"))
        .andExpect(status().isOk());




import static org.assertj.core.api.Assertions.assertThat;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.ActiveProfiles;

import java.util.Optional;

@DataJpaTest
@ActiveProfiles("test")  // テスト時にapplication-test.ymlを使う
public class OrderRepositoryPostgresTest {

    @Autowired
    private OrderRepository orderRepository;

    @Test
    public void testSaveAndRetrieveOrder() {
        // テストデータ作成
        OrderEntity order = new OrderEntity();
        order.setProductName("PostgreSQL Test Product");
        order.setQuantity(20);
        order.setPrice(1000.0);

        // 保存
        OrderEntity savedOrder = orderRepository.save(order);

        // 取得して確認
        Optional<OrderEntity> retrievedOrder = orderRepository.findById(savedOrder.getId());
        assertThat(retrievedOrder).isPresent();
        assertThat(retrievedOrder.get().getProductName()).isEqualTo("PostgreSQL Test Product");
        assertThat(retrievedOrder.get().getQuantity()).isEqualTo(20);
        assertThat(retrievedOrder.get().getPrice()).isEqualTo(1000.0);
    }
}
