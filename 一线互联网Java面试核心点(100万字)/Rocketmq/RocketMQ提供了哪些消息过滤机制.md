# RocketMQ提供了哪些消息过滤机制

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ提供了多种消息过滤机制，以便消费者能够根据业务需求进行精确的消息消费。主要的过滤机制包括：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">Tag过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：最常用的过滤方式，消费者可以基于消息的Tag进行过滤。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">SQL92语法过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：基于Message属性，以SQL92标准语法进行复杂条件过滤，该功能需要Broker支持。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. Tag过滤</font>
**<font style="color:rgba(0, 0, 0, 0.82);">Tag过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">是RocketMQ最基本的过滤机制，性能最好，因为它是在服务器端完成的。</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">每条消息可以有一个或多个Tag。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">消费者订阅时指定需接收的Tag。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">生产者发送带Tag的消息：</font>

```java
import org.apache.rocketmq.client.producer.DefaultMQProducer;  
import org.apache.rocketmq.common.message.Message;  

public class ProducerWithTagExample {  
    public static void main(String[] args) throws Exception {  
        // 创建一个生产者实例  
        DefaultMQProducer producer = new DefaultMQProducer("ProducerGroupName");  
        producer.setNamesrvAddr("localhost:9876");  
        producer.start();  

        // 发送消息，设置不同的Tags  
        String[] tags = {"TagA", "TagB", "TagC"};  
        for (int i = 0; i < 10; i++) {  
            String tag = tags[i % tags.length];  
            Message msg = new Message("TopicTest", tag, ("Hello RocketMQ " + i).getBytes());  
            producer.send(msg);  
        }  

        producer.shutdown();  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">消费者只接收某些Tag的消息：</font>

```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;  
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;  
import org.apache.rocketmq.common.message.MessageExt;  

import java.util.List;  

public class ConsumerWithTagExample {  
    public static void main(String[] args) throws Exception {  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroupName");  
        consumer.setNamesrvAddr("localhost:9876");  

        // 订阅包含TagA和TagB的消息  
        consumer.subscribe("TopicTest", "TagA || TagB");  

        consumer.registerMessageListener(new MessageListenerConcurrently() {  
            @Override  
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
                for (MessageExt msg : msgs) {  
                    System.out.printf("Received message: %s, Tag: %s%n", new String(msg.getBody()), msg.getTags());  
                }  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            }  
        });  

        consumer.start();  
        System.out.println("Consumer started.");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">2. SQL92语法过滤</font>
**<font style="color:rgba(0, 0, 0, 0.82);">SQL92过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">基于用户设置的消息属性，支持更复杂的过滤条件。这种过滤机制是由服务器端进行的，需要预先配置Broker支持属性过滤。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">使用方法</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">启动Broker时启用过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：启动Broker时，需要配置</font>`<font style="color:rgba(0, 0, 0, 0.82);">enablePropertyFilter=true</font>`<font style="color:rgba(0, 0, 0, 0.82);">来启用SQL92语法过滤功能。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">生产者设置消息属性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
import org.apache.rocketmq.client.producer.DefaultMQProducer;  
import org.apache.rocketmq.common.message.Message;  

public class ProducerWithPropertyExample {  
    public static void main(String[] args) throws Exception {  
        DefaultMQProducer producer = new DefaultMQProducer("ProducerGroupName");  
        producer.setNamesrvAddr("localhost:9876");  
        producer.start();  

        for (int i = 0; i < 10; i++) {  
            Message msg = new Message("TopicTest", "TagA", ("Hello RocketMQ " + i).getBytes());  
            msg.putUserProperty("key", String.valueOf(i));  
            producer.send(msg);  
        }  

        producer.shutdown();  
    }  
}
```

1. **<font style="color:rgba(0, 0, 0, 0.82);">消费者使用SQL92语法过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;  
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;  
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;  
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;  
import org.apache.rocketmq.common.message.MessageExt;  

import java.util.List;  

public class ConsumerWithSQL92Example {  
    public static void main(String[] args) throws Exception {  
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroupName");  
        consumer.setNamesrvAddr("localhost:9876");  

        // 使用SQL92条件过滤消息  
        consumer.subscribe("TopicTest", MessageSelector.bySql("key between 0 and 5"));  

        consumer.registerMessageListener(new MessageListenerConcurrently() {  
            @Override  
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {  
                for (MessageExt msg : msgs) {  
                    System.out.printf("Received message: %s, Property key: %s%n", new String(msg.getBody()), msg.getUserProperty("key"));  
                }  
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;  
            }  
        });  

        consumer.start();  
        System.out.println("Consumer started.");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">Tag过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：简单高效，适合多订阅者需接受不同子类型消息的场景。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">SQL92过滤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：灵活强大，适合需要复杂条件过滤的场景，但是需要对性能加以考虑，并且需要在Broker端配置以支持属性过滤。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过提供这些过滤机制，RocketMQ可以大大优化消息消费的准确性和有效性，使得消息系统更能贴合业务需求。不同的机制有各自的优势，选择具体的过滤机制需根据业务需求和系统性能进行合理权衡。</font>



> 更新: 2024-08-18 20:23:07  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/quouet2xt6b2k15h>