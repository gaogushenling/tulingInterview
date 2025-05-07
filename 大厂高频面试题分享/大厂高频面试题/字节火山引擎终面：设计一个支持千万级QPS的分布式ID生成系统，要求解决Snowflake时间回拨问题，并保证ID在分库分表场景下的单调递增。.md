# 字节火山引擎终面：设计一个支持千万级QPS的分布式ID生成系统，要求解决Snowflake时间回拨问题，并保证ID在分库分表场景下的单调递增。

<font style="color:rgb(36, 41, 47);"></font>

### **<font style="color:rgb(36, 41, 47);">一、核心架构设计</font>**
#### <font style="color:rgb(36, 41, 47);">1.</font><font style="color:rgb(36, 41, 47);"> </font>**<font style="color:rgb(36, 41, 47);">位分配优化</font>**
+ **<font style="color:rgb(36, 41, 47);">时间戳（44位）</font>**<font style="color:rgb(36, 41, 47);"> </font><font style="color:rgb(36, 41, 47);">：使用毫秒级时间戳（支持约557年），可改用更高精度时间单位（如0.1ms）以支持更高并发</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">机器ID（8位</font>**<font style="color:rgb(36, 41, 47);">）：可支持256台机器，通过动态注册中心（如ZooKeeper）分配唯一ID</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">序列号（10位）</font>**<font style="color:rgb(36, 41, 47);"> ：每毫秒生成1024个ID，可提升至12位（4096个）以应对更高并发 </font>

<font style="color:rgb(36, 41, 47);"></font>

#### <font style="color:rgb(36, 41, 47);">2.</font><font style="color:rgb(36, 41, 47);"> </font>**<font style="color:rgb(36, 41, 47);">多级发号器部署</font>**
+ **<font style="color:rgb(36, 41, 47);">分片化部署</font>**<font style="color:rgb(36, 41, 47);">：将ID生成服务按业务分片，每个分片独立使用改进的Snowflake算法，确保分片内ID单调递增</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">容器化与水平扩展</font>**<font style="color:rgb(36, 41, 47);">：通过K8s动态扩缩容，结合负载均衡应对QPS波动</font>

<font style="color:rgb(36, 41, 47);"></font>

### **<font style="color:rgb(36, 41, 47);">二、时间回拨解决方案</font>**
#### <font style="color:rgb(36, 41, 47);">1.</font><font style="color:rgb(36, 41, 47);"> </font>**<font style="color:rgb(36, 41, 47);">物理时钟与逻辑时钟结合</font>**
+ **<font style="color:rgb(36, 41, 47);">物理时钟同步</font>**<font style="color:rgb(36, 41, 47);">：部署NTP服务器集群并设置严格时钟同步策略，减少回拨概率</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">逻辑时钟兜底</font>**<font style="color:rgb(36, 41, 47);">：每次生成ID时记录逻辑时间戳（</font>`<font style="color:rgb(36, 41, 47);">last_timestamp</font>`<font style="color:rgb(36, 41, 47);">），当物理时间回拨时，基于逻辑时间戳继续生成ID，并触发告警</font><font style="color:rgb(36, 41, 47);"> </font>

#### <font style="color:rgb(36, 41, 47);">2.</font><font style="color:rgb(36, 41, 47);"> </font>**<font style="color:rgb(36, 41, 47);">回拨容错机制</font>**
+ **<font style="color:rgb(36, 41, 47);">轻度回拨（<阈值）</font>**<font style="color:rgb(36, 41, 47);"> </font><font style="color:rgb(36, 41, 47);">：等待时钟追赶，期间使用逻辑时钟递增值填充</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">重度回拨（>阈值）</font>**<font style="color:rgb(36, 41, 47);"> ：暂停服务并告警，切换备用生成节点 </font>

<font style="color:rgb(36, 41, 47);"></font>

### **<font style="color:rgb(36, 41, 47);">三、分库分表单调递增保障</font>**
#### <font style="color:rgb(36, 41, 47);">1.</font><font style="color:rgb(36, 41, 47);"> </font>**<font style="color:rgb(36, 41, 47);">分片基因嵌入</font>**
+ **<font style="color:rgb(36, 41, 47);">ID高位嵌入分片键</font>**<font style="color:rgb(36, 41, 47);">：将分库分表的Shard Key编码到机器ID中，确保同一分片的所有ID由同一生成器产生，保证分片内严格递增</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">定制位分配</font>**<font style="color:rgb(36, 41, 47);">：例如前8位为分片ID，中间44位为时间戳+序列号，后12位为机器ID</font><font style="color:rgb(36, 41, 47);"> </font>

#### <font style="color:rgb(36, 41, 47);">2.</font><font style="color:rgb(36, 41, 47);"> </font>**<font style="color:rgb(36, 41, 47);">全局趋势递增</font>**
+ **<font style="color:rgb(36, 41, 47);">时间戳高位优先</font>**<font style="color:rgb(36, 41, 47);">：确保ID整体按时间趋势递增，符合分库分表范围查询需求</font>

<font style="color:rgb(36, 41, 47);"></font>

### **<font style="color:rgb(36, 41, 47);">四、示例代码（Java）</font>**
```java
public class EnhancedSnowflake {
    private final long shardIdBits = 8L;       // 分片ID占8位
    private final long sequenceBits = 12L;     // 序列号占12位
    private final long shardIdShift = sequenceBits;
    private final long timestampShift = shardIdBits + sequenceBits;
    private final long maxShardId = \~(-1L << shardIdBits);
    private final long maxSequence = \~(-1L << sequenceBits);

    private long shardId;          // 分片ID（嵌入机器ID）
    private long sequence = 0L;    // 序列号
    private long lastTimestamp = -1L;

    public synchronized long nextId() {
        long timestamp = timeGen();
        
        // 处理时间回拨
        if (timestamp < lastTimestamp) {
            long offset = lastTimestamp - timestamp;
            if (offset <= 5) {  // 允许5ms内的回拨，等待追赶
                try {
                    wait(offset << 1);
                    timestamp = timeGen();
                } catch (InterruptedException e) {
                    throw new RuntimeException("Clock moved backwards");
                }
            } else {  // 严重回拨，切换生成节点
                throw new RuntimeException("Clock moved backwards beyond threshold");
            }
        }

        if (timestamp == lastTimestamp) {
            sequence = (sequence + 1) & maxSequence;
            if (sequence == 0) {  // 当前毫秒序列号用尽，轮询到下一毫秒
                timestamp = tilNextMillis(lastTimestamp);
            }
        } else {
            sequence = 0L;
        }

        lastTimestamp = timestamp;
        return ((timestamp) << timestampShift) | (shardId << shardIdShift) | sequence;
    }

    private long tilNextMillis(long lastTimestamp) {
        long timestamp = timeGen();
        while (timestamp <= lastTimestamp) {
            timestamp = timeGen();
        }
        return timestamp;
    }

    private long timeGen() {
        return System.currentTimeMillis();
    }
}

```



### **<font style="color:rgb(36, 41, 47);">五、性能优化</font>**
1. **<font style="color:rgb(36, 41, 47);">预生成ID缓存</font>**<font style="color:rgb(36, 41, 47);">：批量生成ID并缓存到本地内存，减少实时生成压力</font><font style="color:rgb(36, 41, 47);"> </font>
2. **<font style="color:rgb(36, 41, 47);">无锁化设计</font>**<font style="color:rgb(36, 41, 47);">：使用</font>`<font style="color:rgb(36, 41, 47);">ThreadLocal</font>`<font style="color:rgb(36, 41, 47);">或</font>`<font style="color:rgb(36, 41, 47);">AtomicLong</font>`<font style="color:rgb(36, 41, 47);">优化并发控制</font><font style="color:rgb(36, 41, 47);"> </font>
3. **<font style="color:rgb(36, 41, 47);">服务熔断与降级</font>**<font style="color:rgb(36, 41, 47);">：在时钟异常或QPS超限时降级为UUID模式 </font>

<font style="color:rgb(36, 41, 47);"></font>

### **<font style="color:rgb(36, 41, 47);">六、配套工具</font>**
+ **<font style="color:rgb(36, 41, 47);">监控告警</font>**<font style="color:rgb(36, 41, 47);">：实时监控时钟偏差、序列号使用率、节点负载</font><font style="color:rgb(36, 41, 47);"> </font>
+ **<font style="color:rgb(36, 41, 47);">动态注册中心</font>**<font style="color:rgb(36, 41, 47);">：通过ZooKeeper或Etcd分配机器ID，避免手动配置 </font>

<font style="color:rgb(36, 41, 47);"></font>

### **<font style="color:rgb(36, 41, 47);">总结</font>**
<font style="color:rgb(36, 41, 47);">通过优化Snowflake位分配、结合物理/逻辑时钟解决回拨、嵌入分片基因保证单调递增，配合水平扩展与预生成机制，可达到千万级QPS。实际落地可参考美团Leaf方案，并结合业务特性调整参数。</font>



> 更新: 2025-03-10 13:00:18  
> 原文: <https://www.yuque.com/u12222632/as5rgl/ukftcx3w90fs2asl>