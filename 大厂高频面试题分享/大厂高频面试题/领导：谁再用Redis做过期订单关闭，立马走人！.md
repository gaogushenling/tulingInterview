# 领导：谁再用Redis做过期订单关闭，立马走人！

## <font style="color:rgb(255, 255, 255);background-color:rgb(19, 139, 222);">  
</font>前言
<font style="color:rgb(62, 62, 62);">在电商、支付等领域，往往会有这样的场景，用户下单后放弃支付了，那这笔订单会在指定的时间段后进行关闭操作，细心的你一定发现了像某宝、某东都有这样的逻辑，而且时间很准确，误差在1s内；那他们是怎么实现的呢？</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

<font style="color:rgb(62, 62, 62);">一般实现的方法有几种：</font>

+ <font style="color:rgb(62, 62, 62);">使用 rocketmq、rabbitmq、pulsar 等消息队列的延时投递功能</font>
+ <font style="color:rgb(62, 62, 62);">使用 redisson 提供的 DelayedQueue</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

<font style="color:rgb(62, 62, 62);">有一些方案虽然广为流传但存在着致命缺陷，不要用来实现延时任务</font>

+ <font style="color:rgb(62, 62, 62);">使用 redis 的过期监听</font>
+ <font style="color:rgb(62, 62, 62);">使用 rabbitmq 的死信队列</font>
+ <font style="color:rgb(62, 62, 62);">使用非持久化的时间轮</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

## redis 过期监听
<font style="color:rgb(62, 62, 62);">在 Redis 官方手册的keyspace-notifications: timing-of-expired-events中明确指出：</font>

> <font style="color:rgb(127, 127, 127);">Basically expired events are generated when the Redis server deletes the key and not when the time to live theoretically reaches the value of zero</font>
>

<font style="color:rgb(62, 62, 62);">redis 自动过期的实现方式是：定时任务离线扫描并删除部分过期键；在访问键时惰性检查是否过期并删除过期键。redis 从未保证会在设定的过期时间立即删除并发送过期通知。实际上，过期通知晚于设定的过期时间数分钟的情况也比较常见。</font>

<font style="color:rgb(62, 62, 62);">此外键空间通知采用的是发送即忘(fire and forget)策略，并不像消息队列一样保证送达。当订阅事件的客户端会丢失所有在断线期间所有分发给它的事件。</font>

<font style="color:rgb(62, 62, 62);">这是一种比定时扫描数据库更 “LOW” 的解决方案，请不要使用。</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

## rabbitmq 死信
<font style="color:rgb(62, 62, 62);">死信(Dead Letter) 是 rabbitmq 提供的一种机制。当一条消息满足下列条件之一那么它会成为死信：</font>

+ <font style="color:rgb(62, 62, 62);">消息被否定确认（如channel.basicNack) 并且此时requeue 属性被设置为false</font>
+ <font style="color:rgb(62, 62, 62);">消息在队列的存活时间超过设置的TTL时间</font>
+ <font style="color:rgb(62, 62, 62);">消息队列的消息数量已经超过最大队列长度</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

<font style="color:rgb(62, 62, 62);">若配置了死信队列，死信会被 rabbitmq 投到死信队列中。</font>

<font style="color:rgb(62, 62, 62);">在 rabbitmq 中创建死信队列的操作流程大概是：</font>

+ <font style="color:rgb(62, 62, 62);">创建一个交换机作为死信交换机</font>
+ <font style="color:rgb(62, 62, 62);">在业务队列中配置 x-dead-letter-exchange 和 x-dead-letter-routing-key，将第一步的交换机设为业务队列的死信交换机</font>
+ <font style="color:rgb(62, 62, 62);">在死信交换机上创建队列，并监听此队列</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

<font style="color:rgb(62, 62, 62);">死信队列的设计目的是为了存储没有被正常消费的消息，便于排查和重新投递。死信队列同样也没有对投递时间做出保证，在第一条消息成为死信之前，后面的消息即使过期也不会投递为死信。</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

<font style="color:rgb(62, 62, 62);">为了解决这个问题，rabbit 官方推出了延迟投递插件 rabbitmq-delayed-message-exchange ，推荐使用官方插件来做延时消息。</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

> <font style="color:rgb(127, 127, 127);">这里说点题外话，使用 redis 过期监听或者 rabbitmq 死信队列做延时任务都是以设计者预想之外的方式使用中间件，这种出其不意必自毙的行为通常会存在某些隐患，比如缺乏一致性和可靠性保证，吞吐量较低、资源泄漏等。比较出名的一个事例是很多人使用 redis 的 list 作为消息队列，以致于最后作者看不下去写了 disque 并最后演变为 redis stream。工作中还是尽量不要滥用中间件，用专业的组件做专业的事。</font>
>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

## 时间轮
<font style="color:rgb(62, 62, 62);">时间轮是一种很优秀的定时任务的数据结构，然而绝大多数时间轮实现是纯内存没有持久化的。运行时间轮的进程崩溃之后其中所有的任务都会灰飞烟灭，所以奉劝各位勇士谨慎使用。</font>

## redisson delayqueue
<font style="color:rgb(62, 62, 62);">redisson delayqueue 是一种基于 redis zset 结构的延时队列实现。delayqueue 中有一个名为 timeoutSetName 的有序集合，其中元素的 score 为投递时间戳。delayqueue 会定时使用 zrangebyscore 扫描已到投递时间的消息，然后把它们移动到就绪消息列表中。</font>

<font style="color:rgb(62, 62, 62);">delayqueue 保证 redis 不崩溃的情况下不会丢失消息，在没有更好的解决方案时不妨一试。</font>

<font style="color:rgb(62, 62, 62);">在数据库索引设计良好的情况下，定时扫描数据库中未完成的订单产生的开销并没有想象中那么大。在使用 redisson delayqueue 等定时任务中间件时可以同时使用扫描数据库的方法作为补偿机制，避免中间件故障造成任务丢失。</font>

<font style="color:rgb(62, 62, 62);"></font>

## <font style="color:rgba(6, 8, 31, 0.88);">方案比较</font>
| **<font style="color:rgba(6, 8, 31, 0.88);">方案</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">优点</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">缺点</font>** |
| --- | --- | --- |
| <font style="color:rgba(6, 8, 31, 0.88);">消息队列</font> | <font style="color:rgba(6, 8, 31, 0.88);">可靠性高，支持高并发</font> | <font style="color:rgba(6, 8, 31, 0.88);">需要额外的基础设施</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">Redisson DelayedQueue</font> | <font style="color:rgba(6, 8, 31, 0.88);">简单易用，基于 Redis</font> | <font style="color:rgba(6, 8, 31, 0.88);">依赖于 Redis 的稳定性</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">Redis 过期监听</font> | <font style="color:rgba(6, 8, 31, 0.88);">实现简单，易于上手</font> | <font style="color:rgba(6, 8, 31, 0.88);">不可靠，可能会延迟，缺乏一致性</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">RabbitMQ 死信队列</font> | <font style="color:rgba(6, 8, 31, 0.88);">可用于排查和重新投递</font> | <font style="color:rgba(6, 8, 31, 0.88);">不保证投递时间，依赖于消息的状态</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">时间轮</font> | <font style="color:rgba(6, 8, 31, 0.88);">高效的定时任务管理</font> | <font style="color:rgba(6, 8, 31, 0.88);">无持久化，进程崩溃后任务丢失</font> |


<font style="color:rgba(0, 0, 0, 0.9);"></font>

## 结论
<font style="color:rgba(6, 8, 31, 0.88);">对于延时任务的实现，推荐使用 </font>**<font style="color:rgba(6, 8, 31, 0.88);">RocketMQ</font>**<font style="color:rgba(6, 8, 31, 0.88);">、</font>**<font style="color:rgba(6, 8, 31, 0.88);">Pulsar</font>**<font style="color:rgba(6, 8, 31, 0.88);"> 等专业消息队列，它们提供了可靠的延时投递功能。</font>

<font style="color:rgba(6, 8, 31, 0.88);">在无法使用专业消息队列的情况下，可以考虑使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">Redisson DelayedQueue</font>**<font style="color:rgba(6, 8, 31, 0.88);">，但需设计补偿机制以应对 Redis 崩溃等情况。</font>

<font style="color:rgba(6, 8, 31, 0.88);">如果没有其他选择，可以使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">时间轮</font>**<font style="color:rgba(6, 8, 31, 0.88);">，但需注意其重启频率和持久化问题。</font>

**<font style="color:rgba(6, 8, 31, 0.88);">注意：</font>****<font style="color:#DF2A3F;">永远不要使用 Redis 的过期监听来实现定时任务</font>**<font style="color:rgba(6, 8, 31, 0.88);">，因为它不具备可靠性和一致性保障，容易导致任务丢失。</font>

**<font style="color:#DF2A3F;">永远不要使用 redis 过期监听实现定时任务。</font>**

**<font style="color:#DF2A3F;"></font>**

<font style="color:rgba(6, 8, 31, 0.88);">希望这篇文章能帮助你更好地理解延时任务的实现方式及其最佳实践！如有疑问，欢迎交流讨论。</font>



> 更新: 2025-02-11 14:24:12  
> 原文: <https://www.yuque.com/u12222632/as5rgl/uxcci9m2kwhf35ey>