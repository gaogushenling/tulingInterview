# 高薪Offer必备！四种限流算法一文通关（美团/阿里真题）

#### **一、面试官最爱问的限流算法题**‌
‌**“请解释计数器、滑动窗口、令牌桶、漏桶算法的区别，并说明适用场景？”**

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

#### **二、四大核心限流算法详解**‌
##### **‌****1. 计数器算法：简单但致命的缺陷**‌
**‌****💡****核心原理**‌

+ <font style="color:rgb(51, 51, 51);">统计</font>‌**固定时间窗口**‌<font style="color:rgb(51, 51, 51);">内的请求数（如1分钟），超阈值直接拒绝‌</font><font style="color:rgb(51, 51, 51);">  
</font>

![1741582385341-5e21ea8b-7c37-470c-8e42-9b751a67afd8.webp](./img/IrrulxXwc_uWh_2M/1741582385341-5e21ea8b-7c37-470c-8e42-9b751a67afd8-084926.webp)

**<font style="color:rgb(37, 41, 51);">假设单位时间(固定时间窗口)是</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">1</font>**`**<font style="color:rgb(37, 41, 51);">秒，限流阀值为</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">3</font>**`**<font style="color:rgb(37, 41, 51);">。在单位时间</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">1</font>**`**<font style="color:rgb(37, 41, 51);">秒内，每来一个请求,计数器就加</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">1</font>**`**<font style="color:rgb(37, 41, 51);">，如果计数器累加的次数超过限流阀值</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">3</font>**`**<font style="color:rgb(37, 41, 51);">，后续的请求全部拒绝。等到</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">1s</font>**`**<font style="color:rgb(37, 41, 51);">结束后，计数器清</font>**`**<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">0</font>**`**<font style="color:rgb(37, 41, 51);">，重新开始计数。如下图：</font>**

![1741582385268-723f5c0d-11a0-411b-8287-966a1c0d5870.webp](./img/IrrulxXwc_uWh_2M/1741582385268-723f5c0d-11a0-411b-8287-966a1c0d5870-203751.webp)

**致命缺点**‌

+ ‌**临界值问题****‌****：窗口切换时可能****‌****2倍流量冲击**‌。比如<font style="color:rgb(77, 77, 77);">我们设定1s内允许通过的请求阈值是100，如果在时间窗口的最后几毫秒发送了99个请求，紧接着又在下一个时间窗口开始时发送了99个请求，那么这个用户其实在一秒显然超过了阈值但并不会被限流。</font>

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>‌**✅****适用场景**‌<font style="color:rgba(0, 0, 0, 0.9);">：低频验证码接口（如短信1分钟1次）‌</font>

##### **‌****2. 滑动窗口算法：精准控流的升级版**‌
‌**💡****核心原理**‌

+ <font style="color:rgb(51, 51, 51);">将大窗口拆分为</font>‌**多个小时间片**‌<font style="color:rgb(51, 51, 51);">（如1分钟=6个10秒窗口），动态淘汰过期数据‌。当滑动窗口的</font>**<font style="color:rgb(37, 41, 51);">格子周期划分的越多，那么滑动窗口的滚动就越平滑，限流的统计就会越精确</font>**<font style="color:rgb(37, 41, 51);">。</font><font style="color:rgb(51, 51, 51);">  
</font>

![1741582385313-93013805-7b65-4e84-99d0-0e699a436f50.webp](./img/IrrulxXwc_uWh_2M/1741582385313-93013805-7b65-4e84-99d0-0e699a436f50-778629.webp)

<font style="color:rgba(0, 0, 0, 0.9);">假设单位时间还是</font>`<font style="color:rgba(0, 0, 0, 0.9);">1</font>`<font style="color:rgba(0, 0, 0, 0.9);">s，滑动窗口算法把它划分为</font>`<font style="color:rgba(0, 0, 0, 0.9);">5</font>`<font style="color:rgba(0, 0, 0, 0.9);">个小周期，也就是滑动窗口（</font>**<font style="color:rgba(0, 0, 0, 0.9);">单位时间</font>**<font style="color:rgba(0, 0, 0, 0.9);">）被划分为</font>`<font style="color:rgba(0, 0, 0, 0.9);">5</font>`<font style="color:rgba(0, 0, 0, 0.9);">个小格子。每格表示</font>`<font style="color:rgba(0, 0, 0, 0.9);">0.2s</font>`<font style="color:rgba(0, 0, 0, 0.9);">。每过</font>`<font style="color:rgba(0, 0, 0, 0.9);">0.2s</font>`<font style="color:rgba(0, 0, 0, 0.9);">，时间窗口就会往右滑动一格。然后呢，每个小周期，都有自己独立的计数器，如果请求是</font>`<font style="color:rgba(0, 0, 0, 0.9);">0.83s</font>`<font style="color:rgba(0, 0, 0, 0.9);">到达的，</font>`<font style="color:rgba(0, 0, 0, 0.9);">0.8~1.0s</font>`<font style="color:rgba(0, 0, 0, 0.9);">对应的计数器就会加</font>`<font style="color:rgba(0, 0, 0, 0.9);">1</font>`<font style="color:rgba(0, 0, 0, 0.9);">。</font>

![1741582385419-1e471630-bf11-42a9-8a3f-35ea7147fc97.webp](./img/IrrulxXwc_uWh_2M/1741582385419-1e471630-bf11-42a9-8a3f-35ea7147fc97-205513.webp)

+ ‌**Sentinel框架**‌<font style="color:rgb(51, 51, 51);">采用滑动窗口实现微服务熔断，精度比固定窗口提升80%‌。</font>
+ <font style="color:rgba(0, 0, 0, 0.9);">  
</font>‌**✅****适用场景**‌<font style="color:rgb(51, 51, 51);">：API接口QPS限流（如订单查询每秒100次）‌</font><font style="color:rgb(51, 51, 51);">  
</font>

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

##### **3. 令牌桶算法：秒杀系统的救命稻草**‌
‌**💡****核心原理**‌

+ **<font style="color:rgb(51, 51, 51);">系统以</font>**‌**恒定速率****‌****<font style="color:rgb(51, 51, 51);">生成令牌</font>**<font style="color:rgb(51, 51, 51);">，请求需抢到令牌才能执行。</font>**<font style="color:rgb(51, 51, 51);">支持突发流量</font>**<font style="color:rgb(51, 51, 51);">，比如秒杀系统</font><font style="color:rgb(51, 51, 51);">瞬间大量请求涌入，令牌桶允许前1秒积累的令牌被快速消耗‌。</font><font style="color:rgb(51, 51, 51);">  
</font><font style="color:rgb(51, 51, 51);">  
</font><font style="color:rgb(51, 51, 51);">  
</font>

![1741582385344-f7768391-d40c-49f8-bb0f-e28a6703d18b.webp](./img/IrrulxXwc_uWh_2M/1741582385344-f7768391-d40c-49f8-bb0f-e28a6703d18b-332702.webp)

<font style="color:rgba(0, 0, 0, 0.9);">令牌桶算法是</font>**<font style="color:rgba(0, 0, 0, 0.9);">一种常用的限流算法</font>**<font style="color:rgba(0, 0, 0, 0.9);">，可以用于限制单位时间内请求的数量。该算法维护一个固定容量的令牌桶，每秒钟会向令牌桶中放入一定数量的令牌。当有请求到来时，如果令牌桶中有足够的令牌，则请求被允许通过并从令牌桶中消耗一个令牌，否则请求被拒绝。</font>

![1741582385666-94d9f468-6ef4-4311-bcb6-79dbdcfd0bbf.webp](./img/IrrulxXwc_uWh_2M/1741582385666-94d9f468-6ef4-4311-bcb6-79dbdcfd0bbf-436392.webp)

‌`<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">Guava</font>`<font style="color:rgb(37, 41, 51);">的</font>`<font style="color:rgb(255, 80, 44);background-color:rgb(255, 245, 245);">RateLimiter</font>`<font style="color:rgb(37, 41, 51);">限流组件，就是基于令牌桶算法实现的。</font>**  
****  
****✅****适用场景****‌**：高并发突发流量（如抢购、直播互动）‌

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

##### ‌**4. 漏桶算法：保护下游的稳压器**‌
‌**💡****核心原理**‌

+ <font style="color:rgb(51, 51, 51);">强制以</font>‌**固定速率**‌<font style="color:rgb(51, 51, 51);">处理请求（如水从漏桶流出），超速请求直接丢弃‌</font><font style="color:rgb(51, 51, 51);">  
</font>

![1741582385683-84951d12-bbc7-4ae4-b150-5700e570efb8.webp](./img/IrrulxXwc_uWh_2M/1741582385683-84951d12-bbc7-4ae4-b150-5700e570efb8-755662.webp)

<font style="color:rgba(0, 0, 0, 0.9);">我们可以把发请求的动作比作成注水到桶中，我们处理请求的过程可以比喻为漏桶漏水。我们往桶中以任意速率流入水，以一定速率流出水。当水超过桶流量则丢弃，因为桶容量是不变的，保证了整体的速率。</font>

![1741582385770-dd0cd4b2-69cd-4b80-84db-8c7be383e695.webp](./img/IrrulxXwc_uWh_2M/1741582385770-dd0cd4b2-69cd-4b80-84db-8c7be383e695-323907.webp)

**Nginx限流模块**‌底层采用漏桶算法，防止下游服务被压垮‌<font style="color:rgb(51, 51, 51);">  
</font>**  
****  
****✅****适用场景**‌：API网关流量整形（如支付接口平滑调用）‌<font style="color:rgb(51, 51, 51);">  
</font>

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

#### **‌****三、四种算法对比表**
| **‌****算法****‌** | **‌****核心优势****‌** | **‌****缺陷****‌** | **‌****适用场景****‌** |
| :--- | :--- | :--- | :--- |
| <font style="color:rgb(51, 51, 51);">计数器</font> | <font style="color:rgb(51, 51, 51);">实现简单</font> | <font style="color:rgb(51, 51, 51);">临界值问题</font> | <font style="color:rgb(51, 51, 51);">低频验证码</font> |
| <font style="color:rgb(51, 51, 51);">滑动窗口</font> | <font style="color:rgb(51, 51, 51);">精度高</font> | <font style="color:rgb(51, 51, 51);">计算复杂度高</font> | <font style="color:rgb(51, 51, 51);">API接口QPS限制</font> |
| <font style="color:rgb(51, 51, 51);">令牌桶</font> | <font style="color:rgb(51, 51, 51);">支持突发流量</font> | <font style="color:rgb(51, 51, 51);">需要维护令牌池</font> | <font style="color:rgb(51, 51, 51);">秒杀、直播等高并发</font> |
| <font style="color:rgb(51, 51, 51);">漏桶</font> | <font style="color:rgb(51, 51, 51);">强制平滑输出</font> | <font style="color:rgb(51, 51, 51);">无法应对突发流量</font> | <font style="color:rgb(51, 51, 51);">API网关流量整形</font> |


<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

#### **四、面试加分技巧**‌
1. ‌**分布式限流方案****‌：**
    - <font style="color:rgba(0, 0, 0, 0.9);">答“Redis+Lua原子计数”比“单机限流”更显技术深度‌</font>
2. ‌**算法组合使用****‌：**
    - <font style="color:rgba(0, 0, 0, 0.9);">举例“网关层用漏桶平滑流量，业务层用令牌桶应对突发”‌</font>
3. **‌****动态调整阈值****‌：**
    - <font style="color:rgb(51, 51, 51);">强调“根据系统实时负载（如CPU、内存、QPS等）</font>‌**自动调节限流阈值”能打动面试官‌**<font style="color:rgb(51, 51, 51);">  
</font>

  
  
**最后的思考：如何实现根据系统CPU负载动态调整限流阈值？**<font style="color:rgb(51, 51, 51);">  
</font>

**动态调整阈值是系统高可用设计的核心。**

以美团外卖为例，午高峰时系统CPU可能飙升至80%，此时自动将订单接口QPS阈值从2000降至1500，优先保障核心交易链路；而在低峰期，阈值恢复甚至上浮，避免资源浪费。

**<font style="color:rgb(0, 0, 0);">  
</font>**

**<font style="color:rgb(0, 0, 0);">如果觉得这篇文章对你有所帮助，欢迎点个 </font>****“在看”****<font style="color:rgba(6, 8, 31, 0.88);"> </font>****<font style="color:rgba(6, 8, 31, 0.88);">或分享给更多的小伙伴！</font>**

**<font style="color:rgba(6, 8, 31, 0.88);">关注公众号「</font>****<font style="color:rgba(6, 8, 31, 0.88);">Fox爱分享</font>****<font style="color:rgba(6, 8, 31, 0.88);">」</font>**<font style="color:rgb(64, 64, 64);">，</font>**<font style="color:rgb(64, 64, 64);">解锁更多精彩内容！</font>**

![1741582385663-783d9a51-bc00-4ac0-b38b-b10a5d61cb76.webp](./img/IrrulxXwc_uWh_2M/1741582385663-783d9a51-bc00-4ac0-b38b-b10a5d61cb76-863617.webp)

  
 



> 更新: 2025-03-10 12:53:21  
> 原文: <https://www.yuque.com/u12222632/as5rgl/qxvh2hx0yxpllhob>