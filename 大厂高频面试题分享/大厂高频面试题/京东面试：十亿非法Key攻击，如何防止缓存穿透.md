# 京东面试：十亿非法Key攻击，如何防止缓存穿透



<font style="color:rgb(64, 64, 64);">防止缓存穿透，尤其是应对十亿级非法Key攻击，需要多层次防御策略的结合。以下是系统性解决方案：</font>

### **<font style="color:rgb(64, 64, 64);">1. 布隆过滤器（Bloom Filter）</font>**
+ **<font style="color:rgb(64, 64, 64);">作用</font>**<font style="color:rgb(64, 64, 64);">：快速判断Key是否存在，拦截大部分非法请求。</font>
+ **<font style="color:rgb(64, 64, 64);">优化</font>**<font style="color:rgb(64, 64, 64);">：</font>
    - <font style="color:rgb(64, 64, 64);">使用分布式布隆过滤器（如Redis的</font>`<font style="color:rgb(64, 64, 64);">RedisBloom</font>`<font style="color:rgb(64, 64, 64);">模块），避免单点瓶颈。</font>
    - <font style="color:rgb(64, 64, 64);">动态调整容量和误判率（例如十亿级Key需至少分配约</font><font style="color:rgb(64, 64, 64);"> </font>**<font style="color:rgb(64, 64, 64);">1.2GB 内存</font>**<font style="color:rgb(64, 64, 64);">，误判率1%）。</font>
    - <font style="color:rgb(64, 64, 64);">结合</font>**<font style="color:rgb(64, 64, 64);">布谷鸟过滤器</font>**<font style="color:rgb(64, 64, 64);">（支持删除操作）应对数据动态变化场景。</font>



### **<font style="color:rgb(64, 64, 64);">2. 缓存空值（Null Caching）</font>**
+ **<font style="color:rgb(64, 64, 64);">机制</font>**<font style="color:rgb(64, 64, 64);">：将不存在的Key结果缓存为</font>`<font style="color:rgb(64, 64, 64);">null</font>`<font style="color:rgb(64, 64, 64);">或特殊标记，并设置较短过期时间（如5-30秒）。</font>
+ **<font style="color:rgb(64, 64, 64);">优化</font>**<font style="color:rgb(64, 64, 64);">：</font>
    - **<font style="color:rgb(64, 64, 64);">内存保护</font>**<font style="color:rgb(64, 64, 64);">：限制空值缓存的最大数量（如LRU淘汰策略），防止内存耗尽。</font>
    - **<font style="color:rgb(64, 64, 64);">动态过期时间</font>**<font style="color:rgb(64, 64, 64);">：针对高频攻击Key设置更短过期时间。</font>



### **<font style="color:rgb(64, 64, 64);">3. 请求校验与清洗</font>**
+ **<font style="color:rgb(64, 64, 64);">规则校验</font>**<font style="color:rgb(64, 64, 64);">：在接入层（如API网关）过滤非法格式的Key（如长度、字符类型）。</font>
+ **<font style="color:rgb(64, 64, 64);">签名校验</font>**<font style="color:rgb(64, 64, 64);">：要求合法请求携带签名（如HMAC），攻击者无法伪造有效签名。</font>
+ **<font style="color:rgb(64, 64, 64);">频率限制</font>**<font style="color:rgb(64, 64, 64);">：基于IP/用户ID限流（如令牌桶算法），拦截高频请求。</font>



### **<font style="color:rgb(64, 64, 64);">4. 异步加载与预热</font>**
+ **<font style="color:rgb(64, 64, 64);">异步回源</font>**<font style="color:rgb(64, 64, 64);">：缓存未命中时，通过消息队列异步加载数据，避免瞬时数据库压力。</font>
+ **<font style="color:rgb(64, 64, 64);">热点预加载</font>**<font style="color:rgb(64, 64, 64);">：结合历史数据预测热点Key，提前缓存。</font>



### **<font style="color:rgb(64, 64, 64);">5. 熔断与降级</font>**
+ **<font style="color:rgb(64, 64, 64);">熔断机制</font>**<font style="color:rgb(64, 64, 64);">：监控数据库负载，当QPS超过阈值时触发熔断，返回默认值或错误。</font>
+ **<font style="color:rgb(64, 64, 64);">降级策略</font>**<font style="color:rgb(64, 64, 64);">：缓存穿透期间，直接返回空值或默认结果，跳过数据库查询。</font>



### **<font style="color:rgb(64, 64, 64);">6. 分层缓存架构</font>**
+ **<font style="color:rgb(64, 64, 64);">本地缓存（L1）</font>**<font style="color:rgb(64, 64, 64);">：在应用层使用Guava Cache或Caffeine，拦截重复非法Key。</font>
+ **<font style="color:rgb(64, 64, 64);">分布式缓存（L2）</font>**<font style="color:rgb(64, 64, 64);">：如Redis集群缓存空值和热点数据。</font>



### **<font style="color:rgb(64, 64, 64);">7. 监控与动态策略</font>**
+ **<font style="color:rgb(64, 64, 64);">实时监控</font>**<font style="color:rgb(64, 64, 64);">：追踪缓存命中率、空值占比、数据库QPS等指标。</font>
+ **<font style="color:rgb(64, 64, 64);">动态调整</font>**<font style="color:rgb(64, 64, 64);">：根据攻击特征自动扩容布隆过滤器、调整限流阈值。</font>



### **<font style="color:rgb(64, 64, 64);">8. 数据库防护</font>**
+ **<font style="color:rgb(64, 64, 64);">分库分表</font>**<font style="color:rgb(64, 64, 64);">：分散请求压力。</font>
+ **<font style="color:rgb(64, 64, 64);">读写分离</font>**<font style="color:rgb(64, 64, 64);">：用从库处理穿透查询，保护主库。</font>
+ **<font style="color:rgb(64, 64, 64);">SQL限流</font>**<font style="color:rgb(64, 64, 64);">：通过数据库中间件（如ProxySQL）限制单Key查询频率。</font>



### **<font style="color:rgb(64, 64, 64);">总结方案</font>**
1. **<font style="color:rgb(64, 64, 64);">接入层</font>**<font style="color:rgb(64, 64, 64);">：签名校验 + 参数格式过滤 + IP限流。</font>
2. **<font style="color:rgb(64, 64, 64);">缓存层</font>**<font style="color:rgb(64, 64, 64);">：布隆过滤器 + 空值缓存（短TTL） + 本地缓存。</font>
3. **<font style="color:rgb(64, 64, 64);">数据库层</font>**<font style="color:rgb(64, 64, 64);">：熔断降级 + 异步队列回源 + 分库分表。</font>



<font style="color:rgb(64, 64, 64);">通过以上组合策略，可有效拦截99%以上的非法请求，确保数据库在高并发攻击下保持稳定。需根据业务场景调整参数（如布隆过滤器容量、空值缓存TTL），并持续监控优化。</font>



> 更新: 2025-02-08 03:30:35  
> 原文: <https://www.yuque.com/u12222632/as5rgl/flx4o0nduuoea2p5>