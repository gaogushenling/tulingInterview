# Kafka如何保证消息可靠？

为了保证消息在传递过程当中，消息不会丢失或者被重复传递，Kafka 设计了非常多的重要机制来保证消息的可靠性。例如

1. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">数据冗余：Kafka通过将消息副本（replica）的方式来实现数据冗余，每个topic都可以配置副本数量，副本数量越多，数据可靠性越高，但会占用更多的存储空间和网络带宽。在 Kafka 中，针对每个 Partition，会选举产生一个 Leader 节点，负责响应客户端的请求，并优先保存消息。而其他节点则作为 Follower 节点，负责备份 Master 节点上的消息。</font>
2. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">消息发送确认机制：Kafka支持对生产者发送过来的数据进行校验，以检查数据的完整性。可以通过设置生产者端的参数（例如：acks）来配置校验方式。配置为 0，则不校验生产者发送的消息是否写入 Broker。配置为 1，则只要消息在 Leader 节点上写入成功后就向生产者返回确认信息。配置为-1 或 all，则会等所有 Broker 节点上写入完成后才向生产者返回确认信息。</font>
3. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">ISR机制：针对每个 Partition，Kafka 会维护一个 ISR 列表，里面记录当前处于同步状态的所有Partition。并通过 ISR 机制确保消息不会在Master 故障时丢失。</font>
4. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">消息持久化：Kafka将消息写入到磁盘上，而不是仅在内存中缓存。这样可以保证即使在系统崩溃的情况下，消息也不会丢失。并且使用零拷贝技术提高消息持久化的性能。</font>
5. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">消费者确认机制：Kafka消费者在处理完消息后会向Kafka broker发送确认消息，表示消息已经被成功处理。如果消费者未发送确认消息，则Kafka broker会保留消息并等待消费者再次拉取。这样可以保证消息被正确处理且不会重复消费。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这些机制的组合确保了 Kafka 中消息的高可靠性和持久性，使得 Kafka 成为可靠的消息传递系统，适用于各种实时数据处理和日志聚合需求。</font>



> 更新: 2023-09-11 15:39:42  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/fyuce7d7zglkpwp5>