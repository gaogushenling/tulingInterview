# RocketMQ的集群架构是怎样的

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ的集群架构设计旨在提高系统的可用性、可靠性和可扩展性。它通过多种组件协同工作，实现消息的生产、存储、分发和消费。以下是关于RocketMQ集群架构及其使用场景的详细说明。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">RocketMQ集群架构</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">NameServer</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">它是一个几乎无状态的节点，可以集群部署用于服务发现。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">NameServer为Producer和Consumer提供路由信息。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">Broker</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Broker负责接收、存储和转发消息。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">它可以分为Master和Slave，支持多对Master-Slave配置以实现高可用。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Master和Slave之间通过同步/异步机制进行数据复制。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">Producer</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">消息生产者负责产生消息，并发送到Broker。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">支持同步和异步发送消息。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">Consumer</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">消息消费者负责从Broker接收消息。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">支持Push和Pull两种消费方式，可以是集群消费或广播消费。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用场景</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">高可用性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通过Master-Slave配置和Broker高可用机制，保证系统在部分节点失效的情况下仍能正常运行。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">消息持久化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">控制消息的存储策略（同步/异步），从而达成更高的可靠性需求。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">负载均衡</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用多NameServer和Broker集群，支持负载均衡，从而支持大规模消息流量。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">弹性扩展</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">可以按需添加NameServer和Broker节点，进行水平扩展。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">创建NameServer集群</font>
<font style="color:rgba(0, 0, 0, 0.82);">NameServer无状态，可以简单地在不同的服务器上启动多个实例：</font>

```java
nohup sh mqnamesrv &
```

<font style="color:rgba(0, 0, 0, 0.82);">多台服务器上重复上述操作。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">配置和启动Broker集群</font>
<font style="color:rgba(0, 0, 0, 0.82);">配置文件（</font>`<font style="color:rgba(0, 0, 0, 0.82);">broker-a.properties</font>`<font style="color:rgba(0, 0, 0, 0.82);">）示例：</font>

```java
brokerClusterName=DefaultCluster  
brokerName=broker-a  
brokerId=0  // 0表示Master，>0表示Slave  
namesrvAddr=localhost:9876;localhost:9877  // 多个NameServer地址
```

<font style="color:rgba(0, 0, 0, 0.82);">启动Broker：</font>

```java
nohup sh mqbroker -c broker-a.properties &
```

#### <font style="color:rgba(0, 0, 0, 0.82);">生产者与消费者代码示例</font>
**<font style="color:rgba(0, 0, 0, 0.82);">生产者：</font>**

```java
import org.apache.rocketmq.client.producer.DefaultMQProducer;  
import org.apache.rocketmq.common.message.Message;  

public class ClusterProducerExample {  
    public static void main(String[] args) throws Exception {  
        DefaultMQProducer producer = new DefaultMQProducer("ClusterProducerGroup");  
        producer.setNamesrvAddr("localhost:9876;localhost:9877");  
        producer.start();  

        for (int i = 0; i < 10; i++) {  
            Message msg = new Message("ClusterTopicTest", "TagA", ("Hello RocketMQ " + i).getBytes());  
            producer.send(msg);  
        }  

        producer.shutdown();  
    }  
}
```

**<font style="color:rgba(0, 0, 0, 0.82);">消费者：</font>**

```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;  
import org.apache.rocketmq.common.message.MessageExt;  

import java.util.List;  

public class ClusterConsumerExample {  
    public static void main(String[] args) throws Exception {  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ClusterConsumerGroup");  
        consumer.setNamesrvAddr("localhost:9876;localhost:9877");  
        consumer.subscribe("ClusterTopicTest", "*");  

        consumer.registerMessageListener(new MessageListenerConcurrently() {  
            @Override  
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
                for (MessageExt msg : msgs) {  
                    System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), new String(msg.getBody()));  
                }  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            }  
        });  

        consumer.start();  
        System.out.printf("Consumer started.%n");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ的集群架构通过NameServer、Broker的灵活组合，实现了高可用、弹性伸缩和负载均衡，适用于各种规模的分布式系统。通过合理配置和使用，RocketMQ可以支持广泛的应用场景，如在线业务系统、异步处理和事件驱动架构。</font>



> 更新: 2024-08-18 20:26:01  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/whblnk2iu56i25po>