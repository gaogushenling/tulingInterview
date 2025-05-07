# RocketMQ的Consumer是如何消费消息的

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ提供了两种主要的消费模式：推送式消费（Push Consumer）和拉取式消费（Pull Consumer）。我们将主要讨论更常用的推送式消费模式，并提供相应的代码示例。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">Consumer消费消息的基本流程</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">实例化Consumer</font>**<font style="color:rgba(0, 0, 0, 0.82);">：创建并配置一个Consumer实例。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">订阅主题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：指定要订阅的主题和标签。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">注册消息监听器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：实现消息处理逻辑。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">启动Consumer</font>**<font style="color:rgba(0, 0, 0, 0.82);">：开始消费消息。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">处理消息</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在监听器中处理接收到的消息。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下是一个基本的Push Consumer的代码示例：</font>

```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;  
import org.apache.rocketmq.common.message.MessageExt;  
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;  

import java.util.List;  

public class SimplePushConsumer {  
    public static void main(String[] args) throws Exception {  
        // 创建一个Consumer实例，并指定Consumer Group  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroupName");  

        // 设置NameServer地址  
        consumer.setNamesrvAddr("localhost:9876");  

        // 设置消费起始位置  
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);  

        // 订阅一个或多个主题，并指定标签筛选表达式  
        consumer.subscribe("TopicTest", "*");  

        // 注册回调函数来处理消息  
        consumer.registerMessageListener(new MessageListenerConcurrently() {  
            @Override  
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,  
                                                            ConsumeConcurrentlyContext context) {  
                for (MessageExt msg : msgs) {  
                    System.out.printf("Received message: %s%n", new String(msg.getBody()));  
                }  
                // 标记消息消费成功  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            }  
        });  

        // 启动Consumer实例  
        consumer.start();  

        System.out.printf("Consumer started.%n");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">详细解释</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">Consumer实例化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">DefaultMQPushConsumer</font>`<font style="color:rgba(0, 0, 0, 0.82);">类创建Consumer实例，并指定Consumer Group。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Consumer Group用于标识一组具有相同角色的Consumer实例。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">设置NameServer地址</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">setNamesrvAddr()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法设置RocketMQ NameServer的地址。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">设置消费起始位置</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">setConsumeFromWhere()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法用于指定从哪里开始消费消息。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">CONSUME_FROM_FIRST_OFFSET</font>`<font style="color:rgba(0, 0, 0, 0.82);">表示从最早的可用消息开始消费。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">订阅主题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">subscribe()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法用于订阅指定的主题。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">第一个参数是主题名称，第二个参数是标签过滤表达式。"*"表示订阅该主题下的所有消息。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">注册消息监听器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">registerMessageListener()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法用于注册一个消息监听器。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">这里使用的是</font>`<font style="color:rgba(0, 0, 0, 0.82);">MessageListenerConcurrently</font>`<font style="color:rgba(0, 0, 0, 0.82);">接口，适用于并发消费场景。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在</font>`<font style="color:rgba(0, 0, 0, 0.82);">consumeMessage()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法中实现具体的消息处理逻辑。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">消息处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在监听器的</font>`<font style="color:rgba(0, 0, 0, 0.82);">consumeMessage()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法中，我们遍历收到的消息列表并打印消息内容。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">返回</font>`<font style="color:rgba(0, 0, 0, 0.82);">ConsumeConcurrentlyStatus.CONSUME_SUCCESS</font>`<font style="color:rgba(0, 0, 0, 0.82);">表示消息已成功消费。</font>
7. **<font style="color:rgba(0, 0, 0, 0.82);">启动Consumer</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">start()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法启动Consumer实例，开始消费消息。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">其他重要概念</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">消费模式</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Push模式：Broker主动将消息推送给Consumer。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Pull模式：Consumer主动从Broker拉取消息。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">消息过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">可以通过标签（Tag）或自定义属性进行消息过滤。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">消费进度</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">RocketMQ会自动管理消费进度，确保消息不会重复消费。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">消费失败处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果消息处理失败，可以返回</font>`<font style="color:rgba(0, 0, 0, 0.82);">ConsumeConcurrentlyStatus.RECONSUME_LATER</font>`<font style="color:rgba(0, 0, 0, 0.82);">，RocketMQ会稍后重试。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">负载均衡</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">同一个Consumer Group中的多个Consumer实例会自动进行负载均衡，分摊消息的消费。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">小结</font>
<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ的Consumer提供了灵活且强大的消息消费机制。通过Push模式，开发者可以方便地实现消息的实时处理。Consumer的配置和使用相对简单，但要注意合理处理消息，包括正确设置消费起始位置、适当的消息过滤、以及妥善处理消费失败的情况。在实际应用中，还需要考虑Consumer的伸缩性、容错性和性能优化等方面。</font>



> 更新: 2024-08-18 21:07:26  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vxnpfud2c295wer5>