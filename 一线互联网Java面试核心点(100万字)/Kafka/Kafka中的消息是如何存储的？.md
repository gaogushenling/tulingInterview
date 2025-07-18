# Kafka中的消息是如何存储的？

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Kafka 中的消息是以文件的方式持久化到磁盘中进行存储的，这是 Kafka 的一个关键特性，确保消息的可靠性和可用性。</font><font style="color:rgb(5, 7, 59);">Kafka中的消息是通过以下方式进行存储的：</font>

1. <font style="color:rgb(5, 7, 59);">Partition 分区：Partition是Kafka中消息存储的基本单位，每个Topic下的消息都会被划分成多个Partition进行管理。每个Partition都是一个有序的、不变的消息队列，消息按照追加的顺序被添加到队列尾部。</font>
2. <font style="color:rgb(5, 7, 59);">Segment 分块：Partition会被进一步划分成多个Segment，Segment是逻辑上的文件组，方便进行数据的管理和查找。每个Segment里都包含多个文件，这些文件名相同且被集合在一起。</font>
3. <font style="color:rgb(5, 7, 59);">文件索引：Segment中的每个文件都有自己的索引文件和数据文件，索引文件存储了当前数据文件的索引信息，而数据文件则存储了当前索引文件名对应的数据信息。</font>
4. <font style="color:rgb(5, 7, 59);">消息偏移：Kafka中的每个消息都会被分配到一个特定的Partition中，然后根据Partition内的Segment划分，被存储到对应的数据文件中。消息的偏移量信息则会被记录在索引文件中。</font>
5. <font style="color:rgb(5, 7, 59);">持久化：Kafka中的每个消息都包含一个64位的偏移量，该偏移量表示消息在Partition中的位置。当消费者读取消息时，可以通过偏移量信息来确定需要从哪个位置开始读取。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Kafka 的消息存储是基于日志文件和分区的，确保了消息的可靠性、持久性和高吞吐量。消息被追加到日志文件中，每个消息都有唯一的偏移量，分区和副本机制保证了数据的冗余存储和可用性。这种设计使 Kafka 成为一个可信赖的消息传递系统，适用于各种实时数据处理、日志聚合和事件驱动应用程序。</font>



> 更新: 2023-09-11 16:19:19  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xwrsftnpg347umko>