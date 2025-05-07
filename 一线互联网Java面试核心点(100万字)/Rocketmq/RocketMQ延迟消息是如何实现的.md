# RocketMQ延迟消息是如何实现的

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ通过特定的延迟级别设计实现延迟消息功能。在RocketMQ中，延迟消息是通过设置消息的延迟级别（Delay Level）来实现的。每个延迟级别对应一个特定的时间段，这样可以让消息在指定的时间之后才被消费。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">实现原理</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">延迟级别</font>**<font style="color:rgba(0, 0, 0, 0.82);">：RocketMQ不支持任意时间的延迟，而是提供了18个固定的延迟级别，从1s，5s，10s，30s，1m，2m，3m到2h不等。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">特殊主题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：所有延迟消息都会先发送到一个特殊的内部主题 SCHEDULE_TOPIC_XXXX。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">定时任务</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Broker会启动一个定时任务，按照延迟时间的先后顺序依次扫描每个延迟级别队列。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">消息转移</font>**<font style="color:rgba(0, 0, 0, 0.82);">：当扫描到期的消息时，会将消息从延迟队列转移到目标主题。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">消费者消费</font>**<font style="color:rgba(0, 0, 0, 0.82);">：消息被转移到目标主题后，消费者就可以正常消费这条消息了。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">下面是一个示例，展示如何在RocketMQ中发送和接收延迟消息：</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">生产者端：发送延迟消息</font>
```java
import org.apache.rocketmq.client.producer.DefaultMQProducer;  
import org.apache.rocketmq.client.producer.SendResult;  
import org.apache.rocketmq.common.message.Message;  

public class DelayMessageProducer {  
    public static void main(String[] args) throws Exception {  
        // 创建生产者实例  
        DefaultMQProducer producer = new DefaultMQProducer("DelayMessageProducerGroup");  
        producer.setNamesrvAddr("localhost:9876");  

        // 启动生产者  
        producer.start();  

        // 创建一条消息  
        Message message = new Message("DelayTopic", "TagA", "This is a delayed message.".getBytes());  

        // 设置延迟级别，如3表示延迟10秒  
        message.setDelayTimeLevel(3);  

        // 发送消息  
        SendResult result = producer.send(message);  
        System.out.printf("Sent Result: %s%n", result);  

        // 关闭生产者  
        producer.shutdown();  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">消费者端：接收消息</font>
```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;  
import org.apache.rocketmq.common.message.MessageExt;  

import java.util.List;  

public class DelayMessageConsumer {  
    public static void main(String[] args) throws Exception {  
        // 创建消费者实例  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("DelayMessageConsumerGroup");  
        consumer.setNamesrvAddr("localhost:9876");  

        // 订阅主题  
        consumer.subscribe("DelayTopic", "*");  

        // 注册消息监听器  
        consumer.registerMessageListener(new MessageListenerConcurrently() {  
            @Override  
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
                for (MessageExt msg : msgs) {  
                    System.out.printf("Received Message: %s, Delay Time: %dms%n", new String(msg.getBody()), (System.currentTimeMillis() - msg.getStoreTimestamp()));  
                }  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            }  
        });  

        // 启动消费者  
        consumer.start();  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">运行步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">启动RocketMQ</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确保RocketMQ的NameServer和Broker已经启动。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">运行生产者</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">DelayMessageProducer</font>`<font style="color:rgba(0, 0, 0, 0.82);">类发送延迟消息。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">运行消费者</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">DelayMessageConsumer</font>`<font style="color:rgba(0, 0, 0, 0.82);">类接收延迟消息。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">注意事项</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">延迟级别配置</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以在</font>`<font style="color:rgba(0, 0, 0, 0.82);">broker.conf</font>`<font style="color:rgba(0, 0, 0, 0.82);">中自定义延迟级别，以满足业务需求。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能影响</font>**<font style="color:rgba(0, 0, 0, 0.82);">：延迟消息可能对性能有影响，尤其是在高负载的情况下。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">精确延迟</font>**<font style="color:rgba(0, 0, 0, 0.82);">：RocketMQ的延迟机制不是精确的，延迟时间为近似值。</font>

<font style="color:rgba(0, 0, 0, 0.82);">这种延迟机制非常适合应用于那些需要延迟处理的场景，比如订单超时关闭、优惠券过期提醒等。确保您的业务逻辑满足延迟级别的要求，并合理规划RocketMQ的资源。</font>



> 更新: 2024-08-18 19:20:19  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ottm7mn0vgxy7qua>