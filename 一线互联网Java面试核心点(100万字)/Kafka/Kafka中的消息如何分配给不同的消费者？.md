# Kafka中的消息如何分配给不同的消费者？

<font style="color:rgb(5, 7, 59);">Kafka中的消息是通过分区（Partition）分配给不同的消费者的。Kafka将每个Topic划分为多个Partition，每个Partition存储一部分消息。消费者通过订阅Topic来消费消息，而Kafka将Partition中的消息按照一定的分配策略分配给消费者组中的不同消费者。</font>

<font style="color:rgb(5, 7, 59);">Kafka提供了多种分区分配策略，用于确定如何将分区分配给消费者。例如：</font>

1. <font style="color:rgb(5, 7, 59);">RoundRobin 轮询策略：Kafka将Partition按照轮询的方式分配给消费者组中的不同消费者，每个消费者依次获得一个Partition，直到所有Partition被分配完毕。当消费者数量发生变化时，Kafka会重新分配Partition。</font>
2. <font style="color:rgb(5, 7, 59);">Range 范围策略：Kafka将Partition按照Range的方式分配给消费者组中的不同消费者，每个消费者负责处理指定范围内的Partition。这种分配方式适用于Topic的Partition数量较少，而消费者数量较多的情况。</font>
3. <font style="color:rgb(5, 7, 59);">Sticky 粘性策略： 尽量保持每个消费者在一段时间内消费相同的分区，以减少分区重新分配的频率</font>

<font style="color:rgb(5, 7, 59);">当消费者处理完一个Partition中的所有消息后，它会向Kafka发送心跳请求，Kafka会将该Partition分配给其他消费者进行处理。这种机制确保了消息在不同的消费者之间负载均衡，并提高了容错性。如果一个消费者出现故障，其他消费者可以继续处理Partition中的消息，而不会导致消息丢失或重复处理。</font>



> 更新: 2023-09-11 16:00:04  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ffqhls0w8y09ym97>