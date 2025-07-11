# RabbitMQ 中有哪几种交换机类型？

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">RabbitMQ 支持多种交换机（Exchange）类型，每种类型都用于不同的消息路由和分发策略：</font>

1. **<font style="background-color:rgb(247, 247, 248);">Direct Exchange</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这种交换机根据消息的路由键（Routing Key）将消息发送到与之完全匹配的队列。只有当消息的路由键与队列绑定时指定的路由键完全相同时，消息才会被路由到队列。这是一种简单的路由策略，适用于点对点通信。</font>

2. **<font style="background-color:rgb(247, 247, 248);">Topic Exchange</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这种交换机根据消息的路由键与队列绑定时指定的路由键模式（通配符）匹配程度，将消息路由到一个或多个队列。路由键可以使用通配符符号 </font>**<font style="background-color:rgb(247, 247, 248);">*</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">（匹配一个单词）和 </font>**<font style="background-color:rgb(247, 247, 248);">#</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">（匹配零个或多个单词），允许更灵活的消息路由。用于发布/订阅模式和复杂的消息路由需求。</font>

3. **<font style="background-color:rgb(247, 247, 248);">Headers Exchange</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这种交换机根据消息的标头信息（Headers）来决定消息的路由，而不是使用路由键。队列和交换机之间的绑定规则是根据标头键值对来定义的，只有当消息的标头与绑定规则完全匹配时，消息才会被路由到队列。适用于需要复杂消息匹配的场景。</font>

4. **<font style="background-color:rgb(247, 247, 248);">Fanout Exchange</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这种交换机将消息广播到与之绑定的所有队列，无论消息的路由键是什么。用于发布/订阅模式，其中一个消息被广播给所有订阅者。</font>

5. **<font style="background-color:rgb(247, 247, 248);">Default Exchange</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这是 RabbitMQ 默认实现的一种交换机，它不需要手动创建。当消息发布到默认交换机时，路由键会被解释为队列的名称，消息会被路由到与路由键名称相同的队列。默认交换机通常用于点对点通信，但不支持复杂的路由策略。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这些不同类型的交换机允许你在 RabbitMQ 中实现各种不同的消息路由和分发策略，以满足不同的应用需求。选择适当的交换机类型对于有效的消息传递非常重要。</font>



> 更新: 2023-09-11 17:57:53  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ym0ptogxv1al6hp4>