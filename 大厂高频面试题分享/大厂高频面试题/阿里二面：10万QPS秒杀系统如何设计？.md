# 阿里二面：10万QPS秒杀系统如何设计？

### **<font style="color:rgb(64, 64, 64);">场景</font>**<font style="color:rgb(64, 64, 64);">：设计一个支持10万QPS的秒杀系统，商品库存100件，要求解决超卖问题，保证高性能和高可用。  
</font>**<font style="color:rgb(64, 64, 64);">问题</font>**<font style="color:rgb(64, 64, 64);">：</font>
1. <font style="color:rgb(64, 64, 64);">如何设计库存扣减的原子性操作？请对比数据库乐观锁、Redis+Lua脚本、分布式锁方案的优劣。</font>
2. <font style="color:rgb(64, 64, 64);">如何应对瞬时流量洪峰？请说明流量削峰的具体实现方案（如队列、令牌桶）。</font>
3. <font style="color:rgb(64, 64, 64);">若使用Redis集群缓存库存，如何避免缓存击穿和集群节点宕机导致的数据不一致？</font>

<font style="color:rgb(64, 64, 64);">如果你这些问题答不上来，大概率会被Pass！</font>

  
 

### **<font style="color:rgb(64, 64, 64);">一、库存扣减：如何实现原子操作？</font>**
#### **<font style="color:rgb(64, 64, 64);">1. 方案对比</font>**
| **方案** | **优点** | **缺点** | **适用场景** |
| --- | --- | --- | --- |
| **<font style="color:rgb(64, 64, 64);">数据库乐观锁</font>** | <font style="color:rgb(64, 64, 64);">实现简单</font> | <font style="color:rgb(64, 64, 64);">高并发下大量失败，性能差</font> | <font style="color:rgb(64, 64, 64);">低并发</font> |
| **<font style="color:rgb(64, 64, 64);">Redis+Lua脚本</font>** | <font style="color:rgb(64, 64, 64);">原子性，性能极佳</font> | <font style="color:rgb(64, 64, 64);">Redis单线程瓶颈</font> | <font style="color:rgb(64, 64, 64);">中高并发</font> |
| **<font style="color:rgb(64, 64, 64);">分布式锁（Redisson）</font>** | <font style="color:rgb(64, 64, 64);">强一致性</font> | <font style="color:rgb(64, 64, 64);">性能较低，复杂度高</font> | <font style="color:rgb(64, 64, 64);">强一致性场景</font> |


**<font style="color:rgb(64, 64, 64);">结论</font>**<font style="color:rgb(64, 64, 64);">：</font>**<font style="color:rgb(64, 64, 64);">Redis+Lua脚本是秒杀场景的最优解</font>**<font style="color:rgb(64, 64, 64);">！</font>

#### **<font style="color:rgb(64, 64, 64);">2. Redis+Lua代码实战</font>**
```plain
-- KEYS[1]：库存Key  ARGV[1]：扣减数量  
local stock = tonumber(redis.call('GET', KEYS[1]))  
if stock >= tonumber(ARGV[1]) then  
    redis.call('DECRBY', KEYS[1], ARGV[1])  
    return 1  -- 扣减成功  
else  
    return 0  -- 库存不足  
end  
```

**<font style="color:rgb(64, 64, 64);">Java调用代码</font>**<font style="color:rgb(64, 64, 64);">：</font>

```plain
public boolean deductStock(String key, int num) {  
    String script = "上述Lua脚本";  
    Long result = jedis.eval(script, Collections.singletonList(key), Collections.singletonList(String.valueOf(num)));  
    return result == 1;  
}  
```

**<font style="color:rgb(64, 64, 64);">关键点</font>**<font style="color:rgb(64, 64, 64);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.9);">Lua脚本在Redis中</font>**<font style="color:rgba(0, 0, 0, 0.9);">原子执行</font>**<font style="color:rgba(0, 0, 0, 0.9);">，避免并发冲突</font>
+ <font style="color:rgba(0, 0, 0, 0.9);">库存Key使用</font>`<font style="color:rgba(0, 0, 0, 0.9);">hash tag</font>`<font style="color:rgba(0, 0, 0, 0.9);">确保所有操作落在同一节点</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

### **<font style="color:rgb(64, 64, 64);">二、流量削峰：如何扛住10万QPS？</font>**
#### **<font style="color:rgb(64, 64, 64);">1. 三级缓冲策略，流量分层过滤</font>**
**<font style="color:rgba(0, 0, 0, 0.9);">第一层：客户端限流</font>**

<font style="color:rgba(0, 0, 0, 0.9);">按钮置灰，CDN缓存静态页面</font>

**<font style="color:rgba(0, 0, 0, 0.9);"></font>**

**<font style="color:rgba(0, 0, 0, 0.9);">第二层：网关层令牌桶</font>**

```plain
// 使用Guava RateLimiter  
RateLimiter limiter = RateLimiter.create(10000); // 每秒1万令牌  
if (!limiter.tryAcquire()) {  
    throw new RuntimeException("抢购人数过多，请重试！");  
}  
```



**<font style="color:rgba(0, 0, 0, 0.9);">第三层：队列异步化</font>**

<font style="color:rgba(0, 0, 0, 0.9);">请求进入RabbitMQ/Kafka队列，后端Worker批量处理</font>

### **<font style="color:rgb(64, 64, 64);">三、Redis高可用：如何避免缓存击穿？</font>**
#### **<font style="color:rgb(64, 64, 64);">1. 缓存击穿解决方案</font>**
+ **<font style="color:rgb(64, 64, 64);">方案一：互斥锁</font>**

```plain

public String getStock(String key) {  
    String stock = jedis.get(key);  
    if (stock == null) {  
        if (jedis.setnx("lock:" + key, "1")) {  // 加分布式锁  
            jedis.expire("lock:" + key, 10);  
            stock = loadFromDB(key);           // 回源数据库  
            jedis.setex(key, 60, stock);       // 写入Redis  
            jedis.del("lock:" + key);  
        } else {  
            Thread.sleep(100);                 // 重试  
            return getStock(key);  
        }  
    }  
    return stock;  
}  
```



+ **<font style="color:rgb(64, 64, 64);">方案二：逻辑过期</font>**
    - <font style="color:rgb(64, 64, 64);">缓存永不过期，后台线程定期更新</font>

#### **<font style="color:rgb(64, 64, 64);">2. Redis集群容灾</font>**
+ **<font style="color:rgb(64, 64, 64);">数据分片</font>**<font style="color:rgb(64, 64, 64);">：采用CRC16哈希分片，分散热点</font>
+ **<font style="color:rgb(64, 64, 64);">主从同步</font>**<font style="color:rgb(64, 64, 64);">：每个分片配置1主2从</font>
+ **<font style="color:rgb(64, 64, 64);">故障转移</font>**<font style="color:rgb(64, 64, 64);">：哨兵模式自动切换主节点</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

### **<font style="color:rgb(64, 64, 64);">总结与面试技巧</font>**
1. **<font style="color:rgb(64, 64, 64);">回答模版</font>**<font style="color:rgb(64, 64, 64);">：</font>
    - <font style="color:rgb(64, 64, 64);">“我的设计分为</font>**<font style="color:rgb(64, 64, 64);">三层</font>**<font style="color:rgb(64, 64, 64);">：前端限流 → 异步队列 → 原子扣减”</font>
    - <font style="color:rgb(64, 64, 64);">“Redis集群采用</font>**<font style="color:rgb(64, 64, 64);">CRC16分片 + 哨兵模式</font>**<font style="color:rgb(64, 64, 64);">，确保99.99%可用性”</font>
2. **<font style="color:rgb(64, 64, 64);">杀手锏话术</font>**<font style="color:rgb(64, 64, 64);">：</font>
    - <font style="color:rgb(64, 64, 64);">“在美团实战中，这套方案扛住了618百万QPS的流量冲击！”</font>
3. **<font style="color:rgb(64, 64, 64);">逼格提升</font>**<font style="color:rgb(64, 64, 64);">：</font>
    - <font style="color:rgb(64, 64, 64);">“如果进一步优化，可以引入</font>**<font style="color:rgb(64, 64, 64);">本地库存分桶</font>**<font style="color:rgb(64, 64, 64);">，降低Redis压力”</font>

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

<font style="color:rgb(64, 64, 64);">你在项目中遇到过哪些高并发坑？评论区吐槽，点赞最高的送《亿级流量网站架构核心技术》PDF</font>

**<font style="color:rgb(64, 64, 64);">关注公众号「Fox爱分享」，领取100万字 面试资料</font>****<font style="color:rgb(64, 64, 64);">，解锁更多硬核干货</font>**

![1739877408735-9a81c4a7-3cf2-4774-b5f7-443983d31913.webp](./img/HjOtJhk7gEyMuCW7/1739877408735-9a81c4a7-3cf2-4774-b5f7-443983d31913-710485.webp)

  
 



> 更新: 2025-02-18 19:17:10  
> 原文: <https://www.yuque.com/u12222632/as5rgl/cmqhk7yxpephgnug>