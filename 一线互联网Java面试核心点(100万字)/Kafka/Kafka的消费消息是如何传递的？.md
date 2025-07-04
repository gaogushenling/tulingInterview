# Kafka的消费消息是如何传递的？

<font style="color:rgb(5, 7, 59);">在Kafka中，消息的传递主要涉及三个环节：生产者生产消息、broker保存消息和消费者消费消息。</font>

1. <font style="color:rgb(5, 7, 59);">生产者生产消息：生产者负责将消息发布到Kafka broker。在发布消息时，生产者需要指定目标主题。消息被写入后，将被存储在指定分区的当前副本中。当发送消息失败时，生产者还会提供确认以及重试机制，以保证消息能够正确的发送到 Broker 上。</font>
2. <font style="color:rgb(5, 7, 59);">broker保存消息：Kafka broker接收到生产者发送的消息后，会将其存储在内部的缓冲区中，等待消费者拉取。当消费者向broker发送拉取请求时，broker会从缓冲区中获取消息并返回给消费者。Kafka broker能够保证消息的可靠性和顺序性，即使在异常情况下（如服务器崩溃），也能够保证消息不会丢失。</font>
3. <font style="color:rgb(5, 7, 59);">消费者消费消息：消费者从Kafka broker中订阅指定的主题，并拉取消息进行消费。消费者可以以同步或异步的方式拉取消息，并对拉取到的消息进行处理。当消费者处理完消息后，会向Kafka broker发送确认消息，表示消息已经被成功处理。这样可以保证消息被正确处理且不会重复消费。</font>

<font style="color:rgb(5, 7, 59);">总体来说，Kafka通过生产者、Kafka broker和消费者的协同工作，实现了高吞吐量、高可靠性和高可扩展性的消息传递。</font>



> 更新: 2023-09-11 15:29:50  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vhkyrlrvla5wgc4s>