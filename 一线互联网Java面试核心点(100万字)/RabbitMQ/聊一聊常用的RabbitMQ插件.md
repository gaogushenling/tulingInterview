# 聊一聊常用的 RabbitMQ 插件

<font style="color:rgb(0, 0, 0);">RabbitMQ 支持许多插件，这些插件可以扩展 RabbitMQ 的功能和特性。以下是一些常用的 RabbitMQ 插件：</font>

1. **<font style="background-color:rgb(247, 247, 248);">Management Plugin</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">RabbitMQ 管理插件提供了一个 Web 管理界面，用于监控和管理 RabbitMQ 服务器。可以查看队列、交换机、连接、通道等的状态，并进行配置和操作。</font>

2. **<font style="background-color:rgb(247, 247, 248);">Shovel Plugin</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Shovel 插件用于将消息从一个 RabbitMQ 服务器传递到另一个 RabbitMQ 服务器，实现消息复制和跨集群通信。它可以用于实现数据复制、故障恢复、数据中心间同步等。</font>

3. **<font style="background-color:rgb(247, 247, 248);">Federation Plugin</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Federation 插件允许不同 RabbitMQ 集群之间建立联合，实现消息的跨集群传递。这对于构建分布式系统、将消息从一个地理位置传递到另一个地理位置非常有用。</font>

4. **<font style="background-color:rgb(247, 247, 248);">STOMP Plugin</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">STOMP插件允许使用 STOMP 协议与 RabbitMQ 进行通信。这对于使用非 AMQP 协议的客户端与 RabbitMQ 交互非常有用，例如使用 WebSocket 的 Web 应用程序。</font>

5. **<font style="background-color:rgb(247, 247, 248);">Prometheus Plugin</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Prometheus 插件用于将 RabbitMQ 的性能指标导出到 Prometheus 监控系统，以便进行性能监控和警报。</font>

6. **<font style="background-color:rgb(247, 247, 248);">Delayed Message Plugin</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">延迟消息插件允许发布延迟交付的消息，使你能够在稍后的时间点将消息传递给消费者。这对于实现定时任务、延迟重试等场景非常有用。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这些插件提供了丰富的功能扩展，可以根据需求选择并配置它们，</font><font style="color:rgb(0, 0, 0);">  
</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">  
  
</font>

  




> 更新: 2023-09-12 13:38:31  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hy8iyk9a429atmbl>