# RocketMQ消息积压问题如何解决

<font style="color:rgba(0, 0, 0, 0.82);">消息积压是消息中间件中常见的问题，主要由消费速度跟不上生产速度导致。以下是几种解决方案：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">增加消费者线程数量</font>**

<font style="color:rgba(0, 0, 0, 0.82);">这是最直接的方法，通过增加消费者线程数来提高消费能力。</font>

<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>

```java
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroupName");  
consumer.setNamesrvAddr("nameserver1:9876;nameserver2:9876");  
consumer.setConsumeThreadMin(20);  
consumer.setConsumeThreadMax(64);  

// 设置每个消费者实例消费的最大线程数  
consumer.setConsumeThreadMax(30);  

consumer.subscribe("TopicTest", "*");  
consumer.registerMessageListener(new MessageListenerConcurrently() {  
    @Override  
    public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
        // 消费逻辑  
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
    }  
});  

consumer.start();
```

2. **<font style="color:rgba(0, 0, 0, 0.82);">消息业务异步处理</font>**

<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>

```java
consumer.registerMessageListener(new MessageListenerConcurrently() {  
    @Override  
    public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
        // 批量处理消息  
        executorService.submit(() -> {  
            for (MessageExt msg : msgs) {  
                // 开启后台线程异步处理每条消息  
                processMessageAsync(msg);  
            }  
        });  
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
    }  
});
```

3. **<font style="color:rgba(0, 0, 0, 0.82);">调整消费者的消费模式</font>**

<font style="color:rgba(0, 0, 0, 0.82);">将顺序消费改为并行消费，提高消费效率。</font>

<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>

```java
// 将MessageListenerOrderly改为MessageListenerConcurrently  
consumer.registerMessageListener(new MessageListenerConcurrently() {  
    @Override  
    public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
        // 并行消费逻辑  
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
    }  
});
```

4. **<font style="color:rgba(0, 0, 0, 0.82);">使用消息过滤</font>**

<font style="color:rgba(0, 0, 0, 0.82);">通过消息过滤，只消费重要的消息，降低消费压力。</font>

<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>

```java
consumer.subscribe("TopicTest", "tag1 || tag2 || tag3");
```

5. **<font style="color:rgba(0, 0, 0, 0.82);">临时扩容</font>**

<font style="color:rgba(0, 0, 0, 0.82);">在消息积压严重时，可以临时启动额外的消费者实例来快速消费积压的消息。</font>

<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>

```java
public class TemporaryConsumer {  
    public static void main(String[] args) throws Exception {  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("TemporaryConsumerGroup");  
        consumer.setNamesrvAddr("nameserver:9876");  
        consumer.subscribe("TopicTest", "*");  
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);  
        consumer.registerMessageListener(new MessageListenerConcurrently() {  
            @Override  
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
                // 快速消费逻辑，可以只做简单处理或者将消息转储到其他系统(比如redis)，再启动后台线程处理redis里的消息  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            }  
        });  
        consumer.start();  
    }  
}
```

6. **<font style="color:rgba(0, 0, 0, 0.82);">调整生产者发送策略</font>**

<font style="color:rgba(0, 0, 0, 0.82);">如果可能，可以调整生产者的发送策略，如降低发送频率或者实现背压机制。</font>

<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>

```java
DefaultMQProducer producer = new DefaultMQProducer("ProducerGroupName");  
producer.setNamesrvAddr("nameserver:9876");  
producer.start();  

// 设置发送消息的超时时间，如果超时，说明可能存在积压，可以降低发送频率  
producer.setSendMsgTimeout(3000);  

// 设置异步发送失败重试次数  
producer.setRetryTimesWhenSendAsyncFailed(0);  

// 示例：根据积压情况调整发送频率  
while (true) {  
    if (checkMessageAccumulation(groupName)) {  
        Thread.sleep(1000); // 降低发送频率  
    }  
    Message msg = new Message("TopicTest", "TagA", "Hello RocketMQ".getBytes());  
    producer.send(msg);  
}

public Boolean checkMessageAccumulation(String groupName) throws Exception { 
    private static final long ACCUMULATION_THRESHOLD = 10000; // 消息积压阈值 
    
    DefaultMQAdminExt defaultMQAdminExt = new DefaultMQAdminExt();  
    defaultMQAdminExt.setInstanceName("CheckAccumulationInstance");  

    try {  
        defaultMQAdminExt.start();  
        ConsumeStats consumeStats = defaultMQAdminExt.examineConsumeStats(groupName);  
        //computeTotalDiff方法可以计算当前积压的消息总数
        long accumulateCount = consumeStats.computeTotalDiff();  
        System.out.println("Total accumulated messages: " + accumulateCount);  

        // 根据业务需要设置积压数量阈值  
        if (accumulateCount > ACCUMULATION_THRESHOLD) {  
            System.out.println("发生消息积压");  
            return true;
        }  
    } finally {  
        defaultMQAdminExt.shutdown();  
    }  
    return false;
}  

```

**<font style="color:rgba(0, 0, 0, 0.82);">代码说明：</font>**

`<font style="color:rgba(0, 0, 0, 0.82);">DefaultMQAdminExt</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 是 RocketMQ 提供的一个管理类，属于 RocketMQ 的管理工具模块。它继承自 </font>`<font style="color:rgba(0, 0, 0, 0.82);">MQAdmin</font>`<font style="color:rgba(0, 0, 0, 0.82);">，是用于扩展和增强 RocketMQ 管理能力的一个类。这个类提供了一系列的管理 API，用于管理和监控 RocketMQ 集群，包括消息查询、消费进度管理、主题管理、Broker 状态监控等功能。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">总结：</font>**

<font style="color:rgba(0, 0, 0, 0.82);">这些方法可以单独使用，也可以组合使用，具体取决于您的业务场景和系统架构。在实施这些解决方案时，请注意监控系统性能，确保不会因为过度优化而导致其他问题。同时，也要考虑长期的解决方案，如优化系统架构、升级硬件等，以从根本上提高系统的消息处理能力。</font>



> 更新: 2024-08-19 14:04:32  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ryxgh0hscg5yn8wf>