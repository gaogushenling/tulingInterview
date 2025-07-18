# 什么是 RabbitMQ？有什么显著的特点？

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">RabbitMQ 是一个开源的消息中间件，使用 Erlang 语言开发。这种语言天生非常适合分布式场景，RabbitMQ 也就非常适用于在分布式应用程序之间传递消息。RabbitMQ 有非常多显著的特点：</font>

1. **<font style="background-color:rgb(247, 247, 248);">消息传递模式</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 支持多种消息传递模式，包括发布/订阅、点对点和工作队列等，使其更灵活适用于各种消息通信场景。</font>
2. **<font style="background-color:rgb(247, 247, 248);">消息路由和交换机</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 引入了交换机（Exchange）的概念，用于将消息路由到一个或多个队列。这允许根据消息的内容、标签或路由键进行灵活的消息路由，从而实现更复杂的消息传递逻辑。</font>
3. **<font style="background-color:rgb(247, 247, 248);">消息确认机制</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 支持消息确认机制，消费者可以确认已成功处理消息。这确保了消息不会在传递后被重复消费，增加了消息的可靠性。</font>
4. **<font style="background-color:rgb(247, 247, 248);">可扩展性</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 是高度可扩展的，可以通过添加更多的节点和集群来增加吞吐量和可用性。这使得 RabbitMQ 适用于大规模的分布式系统。</font>
5. **<font style="background-color:rgb(247, 247, 248);">多种编程语言支持</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 提供了多种客户端库和插件，支持多种编程语言，包括 Java、Python、Ruby、Node.js 等，使其在不同技术栈中都能方便地集成和使用。</font>
6. **<font style="background-color:rgb(247, 247, 248);">消息持久性</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 允许消息和队列的持久性设置，确保消息在 RabbitMQ 重新启动后不会丢失。这对于关键的业务消息非常重要。</font>
7. **<font style="background-color:rgb(247, 247, 248);">灵活的插件系统</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 具有丰富的插件系统，使其可以扩展功能，包括管理插件、数据复制插件、分布式部署插件等。</font>
8. **<font style="background-color:rgb(247, 247, 248);">管理界面</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：RabbitMQ 提供了一个易于使用的 Web 管理界面，用于监视和管理队列、交换机、连接和用户权限等。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">总之，RabbitMQ 是一个功能丰富、高度可扩展且灵活的消息中间件，适用于各种分布式应用程序和消息通信需求。它的强大功能和广泛的社区支持使其成为一个流行的消息中间件解决方案。</font>



> 更新: 2023-09-11 17:14:22  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/kdvfcowei9i0qp5l>