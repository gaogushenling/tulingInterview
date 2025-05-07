# RocketMQ如何保证消息不丢失

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ通过多层面的机制来确保消息的可靠性，包括生产者端、broker端和消费者端。下面我将逐一解释这些机制，并提供相应的代码示例。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 生产者端保证</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">a. 同步发送</font>
<font style="color:rgba(0, 0, 0, 0.82);">同步发送是最可靠的发送方式，它会等待broker的确认响应。</font>

```java
DefaultMQProducer producer = new DefaultMQProducer("ProducerGroupName");  
producer.setNamesrvAddr("nameserver:9876");  
producer.start();  

try {  
    Message msg = new Message("TopicTest", "TagA", "Hello RocketMQ".getBytes(RemotingHelper.DEFAULT_CHARSET));  
    SendResult sendResult = producer.send(msg);  
    System.out.printf("消息发送状态: %s%n", sendResult.getSendStatus());  
} catch (Exception e) {  
    e.printStackTrace();  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">b. 异步发送 + 重试机制</font>
<font style="color:rgba(0, 0, 0, 0.82);">异步发送通过回调来处理发送结果，并可以设置重试次数。</font>

```java
producer.setRetryTimesWhenSendAsyncFailed(3);  // 设置异步发送失败重试次数  
producer.send(msg, new SendCallback() {  
    @Override  
    public void onSuccess(SendResult sendResult) {  
        System.out.println("消息发送成功");  
    }  
    @Override  
    public void onException(Throwable e) {  
        System.out.println("消息发送异常，准备重试");  
        e.printStackTrace();  
    }  
});
```

### <font style="color:rgba(0, 0, 0, 0.82);">2. Broker端保证</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">a. 同步刷盘</font>
<font style="color:rgba(0, 0, 0, 0.82);">通过配置</font>`<font style="color:rgba(0, 0, 0, 0.82);">broker.conf</font>`<font style="color:rgba(0, 0, 0, 0.82);">文件，可以启用同步刷盘：</font>

```java
flushDiskType = SYNC_FLUSH
```

#### <font style="color:rgba(0, 0, 0, 0.82);">b. 主从复制</font>
<font style="color:rgba(0, 0, 0, 0.82);">配置主从架构，并设置同步复制：</font>

```java
brokerRole = SYNC_MASTER  
```

### <font style="color:rgba(0, 0, 0, 0.82);">3. 消费者端保证</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">a. 手动提交消费位移</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用手动提交可以确保消息被正确处理后再提交位移。</font>

```java
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroupName");  
consumer.setNamesrvAddr("nameserver:9876");  
consumer.subscribe("TopicTest", "*");  
consumer.setMessageModel(MessageModel.CLUSTERING);  
consumer.registerMessageListener(new MessageListenerConcurrently() {  
    @Override  
    public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
        for (MessageExt msg : msgs) {  
            try {  
                // 处理消息  
                System.out.println(new String(msg.getBody()));  
                // 处理成功后手动提交  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            } catch (Exception e) {  
                // 处理失败，稍后重试  
                return ConsumeConcurrentlyStatus.RECONSUME_LATER;  
            }  
        }  
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
    }  
});  
consumer.start();
```

#### <font style="color:rgba(0, 0, 0, 0.82);">b. 幂等性消费</font>
<font style="color:rgba(0, 0, 0, 0.82);">在消费端实现幂等性处理，确保重复消费不会导致业务问题。</font>

```java
@Override  
public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
    for (MessageExt msg : msgs) {  
        String msgId = msg.getMsgId();  
        if (isProcessed(msgId)) {  
            // 消息已处理，直接返回成功  
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
        }  
        try {  
            // 处理消息  
            processMessage(msg);  
            // 标记消息为已处理  
            markAsProcessed(msgId);  
        } catch (Exception e) {  
            // 处理失败，返回稍后重试  
            return ConsumeConcurrentlyStatus.RECONSUME_LATER;  
        }  
    }  
    return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ通过以下方式保证消息不丢失：</font>

1. <font style="color:rgba(0, 0, 0, 0.82);">生产者端：同步发送、异步发送+重试、事务消息。</font>
2. <font style="color:rgba(0, 0, 0, 0.82);">Broker端：同步刷盘、主从复制。</font>
3. <font style="color:rgba(0, 0, 0, 0.82);">消费者端：手动提交位移、消费重试、幂等性消费。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些机制的组合，RocketMQ能够在各个环节保证消息的可靠性，极大地降低了消息丢失的风险。在实际应用中，可以根据业务需求选择合适的配置和实现方式，以在可靠性和性能之间取得平衡。</font>



> 更新: 2024-08-19 15:40:59  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vg1tvfm5z3u9cggv>