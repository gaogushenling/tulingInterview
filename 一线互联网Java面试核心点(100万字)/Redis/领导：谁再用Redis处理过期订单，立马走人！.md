# 领导：谁再用Redis处理过期订单，立马走人！

## <font style="color:rgba(6, 8, 31, 0.88);">引言</font>
<font style="color:rgba(6, 8, 31, 0.88);">在电商和支付场景中，处理用户订单的延时关闭是至关重要的。然而，有些方案看似简单却潜藏危险，尤其是使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">Redis</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">做过期订单关闭。身为团队的一员，切记：</font>**<font style="color:rgba(6, 8, 31, 0.88);">谁再用Redis处理过期订单，立马走人！</font>**

## <font style="color:rgba(6, 8, 31, 0.88);">不推荐的方案</font>
### <font style="color:rgba(6, 8, 31, 0.88);">1. Redis 过期监听</font>
<font style="color:rgba(6, 8, 31, 0.88);">Redis的过期监听机制并不可靠。官方说明指出，过期事件是在Redis删除键时生成的，而非在TTL到达时立即触发。因此，您可能遇到以下问题：</font>

+ **<font style="color:rgba(6, 8, 31, 0.88);">过期延迟</font>**<font style="color:rgba(6, 8, 31, 0.88);">：过期通知的触发时间会延迟，可能延迟数分钟。</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">“发送即忘”策略</font>**<font style="color:rgba(6, 8, 31, 0.88);">：无法保证消息送达，当客户端断线时会丢失所有分发的事件。</font>

<font style="color:rgba(6, 8, 31, 0.88);">这种不可靠的机制，绝对不适合用于过期订单的管理。</font>

![1728632003658-d48a0914-361e-41cf-832b-7365aabbd6d3.png](./img/GY8mAK6h86byTCiM/1728632003658-d48a0914-361e-41cf-832b-7365aabbd6d3-092035.webp)

### <font style="color:rgba(6, 8, 31, 0.88);">2. RabbitMQ 死信队列</font>
<font style="color:rgba(6, 8, 31, 0.88);">RabbitMQ提供死信机制，要让消息成为死信，必须满足特定条件，例如消息过期或被否定确认。然而，这同样存在时间上的不确定性：</font>

+ **<font style="color:rgba(6, 8, 31, 0.88);">不保证投递时间</font>**<font style="color:rgba(6, 8, 31, 0.88);">：消息在成为死信之前，如果有更早的消息未被处理，则后面的消息无法及时处理。</font>

<font style="color:rgba(6, 8, 31, 0.88);">虽然可以用来排查消息，但它并不理想。</font>

![1728632002991-e67bf6ae-327b-433d-8eaa-84c68a0c44ab.png](./img/GY8mAK6h86byTCiM/1728632002991-e67bf6ae-327b-433d-8eaa-84c68a0c44ab-932552.webp)

### <font style="color:rgba(6, 8, 31, 0.88);">3. 时间轮</font>
<font style="color:rgba(6, 8, 31, 0.88);">时间轮是一种良好的定时任务实现，但大多数实现是纯内存的，没有持久化：</font>

+ **<font style="color:rgba(6, 8, 31, 0.88);">任务丢失风险</font>**<font style="color:rgba(6, 8, 31, 0.88);">：一旦进程崩溃，所有的定时任务都会消失。</font>

<font style="color:rgba(6, 8, 31, 0.88);">在使用时间轮之前，请务必考虑其风险性。</font>

![1735464396958-d8cac87d-f620-4c14-ba8c-1e689275abc3.png](./img/GY8mAK6h86byTCiM/1735464396958-d8cac87d-f620-4c14-ba8c-1e689275abc3-941637.png)

## <font style="color:rgba(6, 8, 31, 0.88);">推荐方案</font>
### <font style="color:rgba(6, 8, 31, 0.88);">使用专业消息队列</font>
<font style="color:rgba(6, 8, 31, 0.88);">对于延时任务，推荐使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">RocketMQ</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">或</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">Pulsar</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">等具有定时投递功能的专业消息队列。这些系统设计上为高并发和可靠性做了优化，确保消息的准确投递。</font>

![1728632002977-70069c25-c236-44e8-bae8-4bf97c56827c.png](./img/GY8mAK6h86byTCiM/1728632002977-70069c25-c236-44e8-bae8-4bf97c56827c-930216.webp)

### <font style="color:rgba(6, 8, 31, 0.88);">Redisson DelayedQueue</font>
<font style="color:rgba(6, 8, 31, 0.88);">如果在特定场景下无法使用专业队列，</font>**<font style="color:rgba(6, 8, 31, 0.88);">Redisson DelayQueue</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">是基于Redis ZSet的一种可选方案。虽然在Redis崩溃时依然可能丢失消息，但在Redis运行稳定的情况下，DelayQueue可以有效避免消息丢失。</font>

<font style="color:rgba(6, 8, 31, 0.88);">同时，请设计补偿机制以应对潜在的Redis崩溃。</font>

![1728632003211-253bc6f4-75a4-49cc-986a-db1646c5ca95.png](./img/GY8mAK6h86byTCiM/1728632003211-253bc6f4-75a4-49cc-986a-db1646c5ca95-474544.webp)



## <font style="color:rgba(6, 8, 31, 0.88);">方案比较</font>
| **<font style="color:rgba(6, 8, 31, 0.88);">方案</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">优点</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">缺点</font>** |
| --- | --- | --- |
| <font style="color:rgba(6, 8, 31, 0.88);">消息队列</font> | <font style="color:rgba(6, 8, 31, 0.88);">可靠性高，支持高并发</font> | <font style="color:rgba(6, 8, 31, 0.88);">需要额外的基础设施</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">Redisson DelayedQueue</font> | <font style="color:rgba(6, 8, 31, 0.88);">简单易用，基于 Redis</font> | <font style="color:rgba(6, 8, 31, 0.88);">依赖于 Redis 的稳定性</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">Redis 过期监听</font> | <font style="color:rgba(6, 8, 31, 0.88);">实现简单，易于上手</font> | <font style="color:rgba(6, 8, 31, 0.88);">不可靠，可能会延迟，缺乏一致性</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">RabbitMQ 死信队列</font> | <font style="color:rgba(6, 8, 31, 0.88);">可用于排查和重新投递</font> | <font style="color:rgba(6, 8, 31, 0.88);">不保证投递时间，依赖于消息的状态</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">时间轮</font> | <font style="color:rgba(6, 8, 31, 0.88);">高效的定时任务管理</font> | <font style="color:rgba(6, 8, 31, 0.88);">无持久化，进程崩溃后任务丢失</font> |




## <font style="color:rgba(6, 8, 31, 0.88);">结论</font>
<font style="color:rgba(6, 8, 31, 0.88);">对于延时任务的实现，推荐使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">RocketMQ</font>**<font style="color:rgba(6, 8, 31, 0.88);">、</font>**<font style="color:rgba(6, 8, 31, 0.88);">Pulsar</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">等专业消息队列，它们提供了可靠的延时投递功能。</font>

<font style="color:rgba(6, 8, 31, 0.88);">在无法使用专业消息队列的情况下，可以考虑使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">Redisson DelayedQueue</font>**<font style="color:rgba(6, 8, 31, 0.88);">，但需设计补偿机制以应对 Redis 崩溃等情况。</font>

<font style="color:rgba(6, 8, 31, 0.88);">如果没有其他选择，可以使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">时间轮</font>**<font style="color:rgba(6, 8, 31, 0.88);">，但需注意其重启频率和持久化问题。</font>

**<font style="color:#DF2A3F;">注意：永远不要使用 Redis 的过期监听来实现定时任务</font>**<font style="color:rgba(6, 8, 31, 0.88);">，因为它不具备可靠性和一致性保障，容易导致任务丢失。</font>

<font style="color:rgba(6, 8, 31, 0.88);">希望这篇文章能帮助你更好地理解延时任务的实现方式及其最佳实践！如有疑问，欢迎交流讨论。</font>



> 更新: 2025-04-15 20:28:11  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xc858dvm27dcanyx>