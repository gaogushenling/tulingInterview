# RocketMQ如何保证消息顺序

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ提供了两种级别的顺序消息：全局顺序消息和分区顺序消息。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 全局顺序消息</font>
<font style="color:rgba(0, 0, 0, 0.82);">全局顺序消息确保一个主题内的所有消息都按照发送顺序被消费。这通常通过将所有消息路由到同一个队列来实现。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">生产者代码示例：</font>
```java
import org.apache.rocketmq.client.producer.DefaultMQProducer;  
import org.apache.rocketmq.client.producer.SendResult;  
import org.apache.rocketmq.common.message.Message;  

public class GlobalOrderProducer {  
    public static void main(String[] args) throws Exception {  
        DefaultMQProducer producer = new DefaultMQProducer("global_order_producer_group");  
        producer.setNamesrvAddr("localhost:9876");  
        producer.start();  

        for (int i = 0; i < 10; i++) {  
            Message msg = new Message("TopicTest", "TagA", ("Hello RocketMQ " + i).getBytes());  
            SendResult sendResult = producer.send(msg,   
                                                  (mqs, msg1, arg) -> mqs.get(0), // 选择第一个队列  
                                                  null);  
            System.out.printf("%s%n", sendResult);  
        }  

        producer.shutdown();  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">消费者代码示例：</font>
```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeOrderlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeOrderlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerOrderly;  
import org.apache.rocketmq.common.message.MessageExt;  

import java.util.List;  

public class GlobalOrderConsumer {  
    public static void main(String[] args) throws Exception {  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("global_order_consumer_group");  
        consumer.setNamesrvAddr("localhost:9876");  
        consumer.subscribe("TopicTest", "*");  

        consumer.registerMessageListener(new MessageListenerOrderly() {  
            @Override  
            public ConsumeOrderlyStatus consumeMessage(List<MessageExt> msgs, ConsumeOrderlyContext context) {  
                for (MessageExt msg : msgs) {  
                    System.out.printf("Consume message: %s%n", new String(msg.getBody()));  
                }  
                return ConsumeOrderlyStatus.SUCCESS;  
            }  
        });  

        consumer.start();  
        System.out.println("Consumer Started.");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">2. 分区顺序消息</font>
<font style="color:rgba(0, 0, 0, 0.82);">分区顺序消息保证具有相同分区键的消息按顺序被消费。这允许更高的并行度，因为不同分区键的消息可以并行处理。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">生产者代码示例：</font>
```java
import org.apache.rocketmq.client.producer.DefaultMQProducer;  
import org.apache.rocketmq.client.producer.SendResult;  
import org.apache.rocketmq.common.message.Message;  

public class PartitionOrderProducer {  
    public static void main(String[] args) throws Exception {  
        DefaultMQProducer producer = new DefaultMQProducer("partition_order_producer_group");  
        producer.setNamesrvAddr("localhost:9876");  
        producer.start();  

        for (int i = 0; i < 100; i++) {  
            int orderId = i % 10; // 使用订单ID作为分区键  
            Message msg = new Message("TopicTest", "TagA",   
                                      "KEY" + i, ("OrderID " + orderId + " Step " + i).getBytes());  
            SendResult sendResult = producer.send(msg, (mqs, msg1, arg) -> {  
                Integer id = (Integer) arg;  
                int index = id % mqs.size();  
                return mqs.get(index);  
            }, orderId);  
            System.out.printf("%s%n", sendResult);  
        }  

        producer.shutdown();  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">消费者代码示例：</font>
```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeOrderlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeOrderlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerOrderly;  
import org.apache.rocketmq.common.message.MessageExt;  

import java.util.List;  
import java.util.concurrent.atomic.AtomicLong;  

public class PartitionOrderConsumer {  
    public static void main(String[] args) throws Exception {  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("partition_order_consumer_group");  
        consumer.setNamesrvAddr("localhost:9876");  
        consumer.subscribe("TopicTest", "TagA");  

        consumer.registerMessageListener(new MessageListenerOrderly() {  
            AtomicLong consumeTimes = new AtomicLong(0);  

            @Override  
            public ConsumeOrderlyStatus consumeMessage(List<MessageExt> msgs, ConsumeOrderlyContext context) {  
                context.setAutoCommit(true);  
                for (MessageExt msg : msgs) {  
                    System.out.printf("Thread: %s, Consume message: %s%n",  
                                      Thread.currentThread().getName(), new String(msg.getBody()));  
                }  
                this.consumeTimes.incrementAndGet();  
                return ConsumeOrderlyStatus.SUCCESS;  
            }  
        });  

        consumer.start();  
        System.out.println("Consumer Started.");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">关键点解释</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">消息队列选择</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">全局顺序：所有消息都发送到同一个队列。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">分区顺序：使用自定义的队列选择算法，确保相同分区键的消息进入同一队列。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">MessageListenerOrderly</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">MessageListenerOrderly</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">接口来确保消息的有序消费。RocketMQ 保证同一个队列的消息会被同一个线程顺序处理。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">ConsumeOrderlyStatus</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">消费者返回</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ConsumeOrderlyStatus.SUCCESS</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">表示消息已成功处理。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">锁定机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">RocketMQ 内部使用锁定机制确保同一时间只有一个消费者线程在处理特定队列的消息。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">重试机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">如果消息处理失败，RocketMQ 会自动重试，同时保持消息的顺序。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">负载均衡</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">分区顺序消息允许更好的负载均衡，因为不同的分区可以并行处理。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">注意事项</font>
1. <font style="color:rgba(0, 0, 0, 0.82);">全局顺序消息可能会限制系统的吞吐量，因为所有消息都经过单一队列。</font>
2. <font style="color:rgba(0, 0, 0, 0.82);">分区顺序消息在保证局部顺序的同时提供了更好的并行性。</font>
3. <font style="color:rgba(0, 0, 0, 0.82);">选择合适的分区键对于分区顺序消息至关重要，以确保相关消息进入同一队列。</font>
4. <font style="color:rgba(0, 0, 0, 0.82);">消费者端需要正确处理并发和重试逻辑，以维护消息顺序。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些机制和实践，RocketMQ 能够在不同场景下有效地保证消息的顺序性，同时兼顾系统的性能和可扩展性。</font>



> 更新: 2024-08-19 16:08:21  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dpli1grw8pt3tdhr>