# Redis的热Key问题如何解决

Redis的热Key问题解决方案需要根据具体情况选择合适的方法。以下是一些常见的解决方案和对应的Java代码示例：  
解决方案

1. **本地缓存**  
使用本地缓存来缓解Redis的压力，从而减少对热Key的直接访问。

```java
import com.github.benmanes.caffeine.cache.Cache;  
import com.github.benmanes.caffeine.cache.Caffeine;  

import java.util.concurrent.TimeUnit;  

public class LocalCacheManager {  
    // 本地内存缓存实例  
    private Cache<String, String> localCache = Caffeine.newBuilder()  
    .expireAfterWrite(10, TimeUnit.MINUTES)  
    .maximumSize(1000)  
    .build();  

    public String getData(String key) {  
        return localCache.get(key, this::loadFromRedis);  
    }  

    private String loadFromRedis(String key) {  
        // 从Redis获取数据的模拟方法  
        return RedisClient.get(key);  
    }  
}
```



2. **请求分摊**

<font style="color:rgba(0, 0, 0, 0.82);">把热Key拆分成多个子Key，这样可以将读请求分摊到多个Key上，从而降低单Key的压力。</font>

```java
import java.util.List;  
import java.util.Random;  

public class KeyDistributor {  
    private static final int NUM_SHARDS = 10;  
    private List<String> shards;  

    public KeyDistributor(List<String> shardKeys) {  
        this.shards = shardKeys;  
    }  

    public String getDistributedKey(String originalKey) {  
        int shardNum = originalKey.hashCode() % NUM_SHARDS;  
        return shards.get(shardNum) + ":" + originalKey;  
    }  
} 
```



3. **限流**  
对热Key的访问进行限流，防止过多请求进入。

```java
import com.google.common.util.concurrent.RateLimiter;  

public class RateLimiterExample {  
    private RateLimiter rateLimiter = RateLimiter.create(10); // 每秒10个请求  

    public String accessResource(String key) {  
        if (rateLimiter.tryAcquire()) {  
            return RedisClient.get(key);  
        } else {  
            return "Rate limit exceeded";  
        }  
    }  
}
```



4. **监控和报警**  
通过设置监控来实时观测Redis的使用情况，及时应对热Key问题，例如通过Redis的INFO命令或使用监控工具。对于不可预知的热Key场景，我们一般来说都会接入我们的**热点探测系统**，定期上报我们对应key的调用次数，有热点探测系统检测是否是热key，然后通过sdk通知各个应用节点快速构建本地缓存，来抗住这些热key带来的流量。



> 更新: 2024-08-07 20:17:25  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/qf8k72bdh2er2mdl>