# RocketMQ生产环境如何优化

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ在生产环境中的优化是一个多方面的任务，涉及到系统配置、应用代码、监控和运维等多个层面。以下是一些关键的优化策略，并附带相关的代码和配置示例：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">生产者优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">a) 批量发送消息  
</font><font style="color:rgba(0, 0, 0, 0.82);">批量发送可以提高吞吐量，减少网络开销。</font>

```java
DefaultMQProducer producer = new DefaultMQProducer("ProducerGroupName");  
producer.setNamesrvAddr("nameserver1:9876;nameserver2:9876");  
producer.start();  

List<Message> messages = new ArrayList<>();  
for (int i = 0; i < 100; i++) {  
    messages.add(new Message("TopicTest", "TagA", "OrderID188", "Hello world".getBytes(StandardCharsets.UTF_8)));  
}  
producer.send(messages);
```

<font style="color:rgba(0, 0, 0, 0.82);">b) 异步发送  
</font><font style="color:rgba(0, 0, 0, 0.82);">对于非关键消息，使用异步发送可以提高发送效率。</font>

```java
producer.send(msg, new SendCallback() {  
    @Override  
    public void onSuccess(SendResult sendResult) {  
        // 处理发送成功  
    }  
    @Override  
    public void onException(Throwable e) {  
        // 处理发送异常  
    }  
});
```

<font style="color:rgba(0, 0, 0, 0.82);">c) 合理设置发送超时时间</font>

```java
producer.setSendMsgTimeout(3000);  // 设置发送超时时间为3秒
```

### <font style="color:rgba(0, 0, 0, 0.82);">消费者优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">a) 增加消费并发度  
</font><font style="color:rgba(0, 0, 0, 0.82);">通过增加消费线程数来提高消费能力。</font>

```java
consumer.setConsumeThreadMin(20);  
consumer.setConsumeThreadMax(64);
```

<font style="color:rgba(0, 0, 0, 0.82);">b) 批量消费  
</font><font style="color:rgba(0, 0, 0, 0.82);">配置批量消费可以减少网络交互，提高效率。</font>

```java
consumer.setConsumeMessageBatchMaxSize(32);  // 设置批量消费，一次最多消费32条
```

<font style="color:rgba(0, 0, 0, 0.82);">c) 合理设置消费重试次数</font>

```java
consumer.setMaxReconsumeTimes(3);  // 设置最大重试次数为3次
```

### <font style="color:rgba(0, 0, 0, 0.82);">Broker配置优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">需要在broker.conf中配置：</font>

```java
brokerClusterName = DefaultCluster  
brokerName = broker-a  
brokerId = 0  
brokerRole = ASYNC_MASTER    #主从异步复制消息
flushDiskType = ASYNC_FLUSH  #开启异步刷盘
```

### <font style="color:rgba(0, 0, 0, 0.82);">合理设置Topic的队列数</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">小规模系统</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对于一般的小型应用，可以从</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>**<font style="color:rgba(0, 0, 0, 0.82);">4 到 8 个队列</font>**<font style="color:rgba(0, 0, 0, 0.82);">开始，根据具体的消息负载和性能监控结果再行调整。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">中等到大型系统</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对于大型系统或高吞吐量需求，默认 </font>**<font style="color:rgba(0, 0, 0, 0.82);">16 到 32 个队列</font>**<font style="color:rgba(0, 0, 0, 0.82);"> 是常见的设置。</font>

```java
producer.setDefaultTopicQueueNums(8);  // 设置默认的队列数
```

### <font style="color:rgba(0, 0, 0, 0.82);">消费设置长轮询模式</font>
<font style="color:rgba(0, 0, 0, 0.82);">长轮询可以减少无效的轮询请求，在消费者端配置：</font>

```java
consumer.setPullInterval(0);  // 设置拉取间隔为0，启用长轮询
```

### <font style="color:rgba(0, 0, 0, 0.82);">JVM优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">a) 设置合适的堆内存大小</font>

```java
-Xms8g -Xmx8g -Xmn2g
```

<font style="color:rgba(0, 0, 0, 0.82);">b) 使用G1垃圾收集器</font>

```java
-XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:G1ReservePercent=25 -XX:InitiatingHeapOccupancyPercent=30
```

### <font style="color:rgba(0, 0, 0, 0.82);">消息存储优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">a) 启用文件预热  
</font><font style="color:rgba(0, 0, 0, 0.82);">在broker.conf中开启文件预热配置，将warmMapedFileEnable这个参数设置为 </font>`<font style="color:rgba(0, 0, 0, 0.82);">true</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 时，RocketMQ 会在创建映射文件时对其进行预热。具体的实现方式可能包括写入一些数据以触发操作系统将文件的物理页与虚拟内存页面相关联，可以减少I/O延迟和提高文件读写效率。</font>

```java
warmMapedFileEnable=true
```

### <font style="color:rgba(0, 0, 0, 0.82);">监控和告警</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用RocketMQ的Dashboard或者其他监控工具（如Prometheus + Grafana）来监控系统状态，并设置适当的告警阈值。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">消息压缩</font>
<font style="color:rgba(0, 0, 0, 0.82);">对于大消息，可以考虑进行压缩：</font>

```java
Message msg = new Message("TopicTest", "TagA", "Hello RocketMQ".getBytes(StandardCharsets.UTF_8));  
msg.setCompressed(true);
```

<font style="color:rgba(0, 0, 0, 0.82);">这些优化策略涵盖了RocketMQ的多个方面，包括生产者、消费者、Broker配置、网络、JVM、存储等。在实际应用中，需要根据具体的业务场景和硬件环境来选择合适的优化策略。同时，优化是一个持续的过程，需要不断监控、测试和调整，以达到最佳的性能表现。</font>



> 更新: 2024-08-19 17:05:09  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/pplrbbzufrv01z8q>