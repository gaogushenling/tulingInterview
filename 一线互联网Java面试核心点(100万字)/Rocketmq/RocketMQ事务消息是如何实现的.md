# RocketMQ事务消息是如何实现的

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ的事务消息对分布式系统中的事务一致性问题提供了有效的解决方案。比如当用户下单时，需要同时处理订单生成、库存扣减、支付处理等操作。事务消息确保所有这些操作要么全部成功，要么全部回滚。</font>

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ的事务消息实现是一个复杂而精巧的过程，主要通过两阶段提交（2PC）和补偿机制来保证分布式事务的一致性。下面我将</font><font style="color:rgba(0, 0, 0, 0.97);">结合下单减库存场景详细解释，这里订单和库存是两个微服务，所以这两步操作存在分布式事务问题，我们可以在下单操作完成发送减库存事务消息异步扣减库存，</font><font style="color:rgba(0, 0, 0, 0.82);">使用 RocketMQ 的事务消息，可以确保订单创建和库存扣减的事务一致性。以下是实现该场景的详细步骤：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">场景描述</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">用户下单</font>**<font style="color:rgba(0, 0, 0, 0.82);">：用户在购物平台上发起一个购买请求。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">减库存操作</font>**<font style="color:rgba(0, 0, 0, 0.82);">：为保证商品库存准确性，在订单创建时需扣减相应的库存。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">事务消息发送</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在创建订单的同时，发送事务消息用于扣减库存。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">实现步骤</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. 准备工作</font>
<font style="color:rgba(0, 0, 0, 0.82);">假设我们有两个服务：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">订单服务（Order Service）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：负责处理订单创建。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">库存服务（Inventory Service）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：负责管理库存更新。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 使用事务消息实现步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">发送半消息</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在订单服务中，首先发送一个库存扣减的半消息到 RocketMQ。这条消息在事务未完成前，对消费者（库存服务）是不可见的。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">执行本地事务（创建订单）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在订单服务中执行本地事务，即在数据库中创建订单记录。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">根据本地事务结果提交或回滚事务消息</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果订单创建成功，提交事务消息，使库存扣减消息对库存服务可见。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果订单创建失败，回滚事务消息，库存不变。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">处理MQ事务消息状态回查</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果RocketMQ未收到事务提交或回滚的结果，会定期询问订单服务，以确认事务的最终状态。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">3. 代码实现</font>
```java
import org.apache.rocketmq.client.exception.MQClientException;  
import org.apache.rocketmq.client.producer.TransactionListener;  
import org.apache.rocketmq.client.producer.TransactionMQProducer;  
import org.apache.rocketmq.client.producer.TransactionSendResult;  
import org.apache.rocketmq.common.message.Message;  
import org.apache.rocketmq.common.message.MessageExt;  

public class OrderService {  

    public static void main(String[] args) throws MQClientException {  
        // 初始化事务消息生产者  
        TransactionMQProducer producer = new TransactionMQProducer("order_transaction_group");  
        producer.setNamesrvAddr("localhost:9876");  

        // 设置事务监听器  
        TransactionListener transactionListener = new OrderTransactionListener();  
        producer.setTransactionListener(transactionListener);  
        producer.start();  

        try {  
            // 构建减库存半消息  
            Message msg = new Message("OrderTopic", "TagA", "KEY1",  
                                      ("OrderID:12345, SKU:67890, Quantity:1").getBytes());  

            // 发送事务消息  
            TransactionSendResult sendResult = producer.sendMessageInTransaction(msg, null);  
            System.out.printf("SendResult: %s%n", sendResult);  

        } catch (MQClientException e) {  
            e.printStackTrace();  
        }  

        Runtime.getRuntime().addShutdownHook(new Thread(producer::shutdown));  
    }  
}  

class OrderTransactionListener implements TransactionListener {  

    @Override  
    public LocalTransactionState executeLocalTransaction(Message msg, Object arg) {  
        try {  
            // 执行本地事务：创建订单  
            boolean isOrderCreated = createOrder(msg);  

            if (isOrderCreated) {  
                return LocalTransactionState.COMMIT_MESSAGE; // 提交库存扣减消息  
            } else {  
                return LocalTransactionState.ROLLBACK_MESSAGE; // 回滚库存扣减消息  
            }  
        } catch (Exception e) {  
            e.printStackTrace();  
            return LocalTransactionState.UNKNOW; // 未知状态  
        }  
    }  

    @Override  
    public LocalTransactionState checkLocalTransaction(MessageExt msg) {  
        // 回查本地事务状态  
        boolean isOrderConfirmed = checkOrderStatus(msg.getKeys());  
        return isOrderConfirmed ? LocalTransactionState.COMMIT_MESSAGE : LocalTransactionState.ROLLBACK_MESSAGE;  
    }  

    private boolean createOrder(Message msg) {  
        // 模拟订单创建逻辑（通常为数据库操作）  
        System.out.println("Executing local transaction to create order: " + new String(msg.getBody()));  
        // 这里应该包括数据库的insert操作和相关的业务逻辑  
        return true; // 返回订单创建成功状态  
    }  

    private boolean checkOrderStatus(String orderKey) {  
        // 模拟查询订单状态  
        System.out.println("Checking order status for key: " + orderKey);  
        // 这里通常涉及查询数据库以确认订单的最终状态  
        return true; // 假设订单已确认  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">实现要点</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">事务消息的发送</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">sendMessageInTransaction</font>`<font style="color:rgba(0, 0, 0, 0.82);">用于发送半消息，该消息在事务未确定前不会被消费者接收。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">本地事务操作</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在</font>`<font style="color:rgba(0, 0, 0, 0.82);">executeLocalTransaction</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法中执行订单创建的本地事务逻辑。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">事务状态反馈</font>**<font style="color:rgba(0, 0, 0, 0.82);">：根据订单创建成功与否，决定是否提交或回滚事务消息。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">事务状态回查</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">checkLocalTransaction</font>`<font style="color:rgba(0, 0, 0, 0.82);">用于处理RocketMQ对事务状态的回查请求，以解决网络超时、进程崩溃等问题导致的事务状态不一致。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">注意事项</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">幂等性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：事务操作（例如订单创建和库存扣减）应确保幂等性，以防止重复执行造成的数据不一致。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">异常处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：正确处理各种异常状况，确保事务的完善性和数据的一致性。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">回查频率</font>**<font style="color:rgba(0, 0, 0, 0.82);">：设置适当的事务回查策略，避免频繁的检查影响性能。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些实现，RocketMQ可以确保在订单创建与覆盖在储存事务消息的可靠性之间的精准一致性处理，保障系统的最终一致性和事务可靠性。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">RocketMQ事务消息的内部实现原理</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. 半事务消息</font>
<font style="color:rgba(0, 0, 0, 0.82);">当生产者发送半事务消息时，RocketMQ会将其标记为"Prepared"状态，并存储在特定的队列中。这种消息对消费者不可见。</font>

```java
// RocketMQ内部实现（简化版）  
public class TransactionMessageService {  
    public PutMessageResult prepareMessage(MessageExtBrokerInner msgInner) {  
        // 将消息标记为Prepared状态  
        msgInner.setTransactionPrepared(true);  
        // 存储消息到特定队列  
        return commitLog.putMessage(msgInner);  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 提交或回滚事务</font>
<font style="color:rgba(0, 0, 0, 0.82);">根据本地事务的执行结果，生产者会向RocketMQ发送提交或回滚指令。</font>

```java
// RocketMQ内部实现（简化版）  
public class TransactionMessageService {  
    public void commitMessage(MessageExt msgExt) {  
        // 将消息从Prepared状态改为可消费状态  
        MessageExtBrokerInner msgInner = convertToMessageInner(msgExt);  
        msgInner.setTransactionPrepared(false);  
        // 将消息从特定队列转存到真正的发送队列里去
        commitLog.putMessage(msgInner);  
    }  

    public void rollbackMessage(MessageExt msgExt) {  
        // 删除Prepared状态的消息  
        removeMessage(msgExt);  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">3. 事务状态回查</font>
<font style="color:rgba(0, 0, 0, 0.82);">如果RocketMQ在一定时间内没有收到提交或回滚指令，会启动回查机制。</font>

```java
// RocketMQ内部实现（简化版）  
public class TransactionCheckService {  
    public void check() {  
        for (MessageExt msgExt : getPrepareedMessages()) {  
            LocalTransactionState state = producer.checkLocalTransaction(msgExt);  
            if (state == LocalTransactionState.COMMIT_MESSAGE) {  
                commitMessage(msgExt);  
            } else if (state == LocalTransactionState.ROLLBACK_MESSAGE) {  
                rollbackMessage(msgExt);  
            } else {  
                // 继续等待  
            }  
        }  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">4. 关键点说明</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">原子性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：半事务消息对消费者不可见，确保了事务的原子性。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">持久性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：半事务消息会被持久化，即使Broker宕机也能恢复。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">隔离性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过特殊的队列存储半事务消息，实现了隔离。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">一致性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过二阶段提交和回查机制保证了最终一致性。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">通过这套实现机制，RocketMQ能够在分布式系统中实现可靠的事务消息，确保消息发送与本地事务的一致性，即使在系统崩溃或网络故障的情况下也能保证数据的一致性。</font>


> 更新: 2024-08-19 15:18:20  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/lplcgplbp8gkekgl>