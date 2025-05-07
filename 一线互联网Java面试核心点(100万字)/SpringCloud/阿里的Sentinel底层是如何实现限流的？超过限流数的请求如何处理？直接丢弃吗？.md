# 阿里的Sentinel底层是如何实现限流的？超过限流数的请求如何处理？直接丢弃吗？

## <font style="color:rgba(6, 8, 31, 0.88);">一、Sentinel的底层限流实现机制</font>
### <font style="color:rgba(6, 8, 31, 0.88);">1. 限流的核心思想</font>
<font style="color:rgba(6, 8, 31, 0.88);">Sentinel主要采用</font>**<font style="color:rgba(6, 8, 31, 0.88);">滑动时间窗口结合计数器</font>**<font style="color:rgba(6, 8, 31, 0.88);">的原理进行流量统计和限流判断，也融合了令牌桶、漏桶等思想。核心目标是</font>**<font style="color:rgba(6, 8, 31, 0.88);">在单位时间内只能通过限定的请求数</font>**<font style="color:rgba(6, 8, 31, 0.88);">，超出则触发限流。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">2. 滑动窗口结构</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">时间窗口划分</font>**<font style="color:rgba(6, 8, 31, 0.88);">：Sentinel将统计时间（如1秒）切成多个格子（如每格100ms），每个格子记录当前格子的请求数。</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">窗口滑动</font>**<font style="color:rgba(6, 8, 31, 0.88);">：每次有请求进来，根据时间戳判断属于哪个格子，并刷新对应计数。多个格子的统计值累加得出单位时间的总请求数。</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">核心数据结构</font>**<font style="color:rgba(6, 8, 31, 0.88);">：底层常用</font>**<font style="color:rgba(6, 8, 31, 0.88);">LeapArray</font>**<font style="color:rgba(6, 8, 31, 0.88);">这类循环队列管理时间窗口，保障统计高并发下的效率和准确度。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">3. 阈值判断</font>
<font style="color:rgba(6, 8, 31, 0.88);">每当有新请求进来时：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">Sentinel会计算当前时间窗口累计的请求数。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">若超过限流阈值，则进入</font>**<font style="color:rgba(6, 8, 31, 0.88);">限流逻辑</font>**<font style="color:rgba(6, 8, 31, 0.88);">。</font>

---

## <font style="color:rgba(6, 8, 31, 0.88);">二、超过限流数的请求如何处理？直接丢弃吗？</font>
<font style="color:rgba(6, 8, 31, 0.88);">Sentinel提供了</font>**<font style="color:rgba(6, 8, 31, 0.88);">多种限流处理策略</font>**<font style="color:rgba(6, 8, 31, 0.88);">，通过配置FlowRule的controlBehavior参数决定。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">1. 常用处理方式</font>
| **<font style="color:rgba(6, 8, 31, 0.88);">controlBehavior</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">处理方式</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">说明</font>** |
| --- | --- | --- |
| <font style="color:rgba(6, 8, 31, 0.88);">0（默认）</font> | <font style="color:rgba(6, 8, 31, 0.88);">直接拒绝</font> | <font style="color:rgba(6, 8, 31, 0.88);">超过阈值的请求立即被拒绝，直接返回异常</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">1</font> | <font style="color:rgba(6, 8, 31, 0.88);">匀速排队（漏桶）</font> | <font style="color:rgba(6, 8, 31, 0.88);">超过阈值的请求以固定速率排队等待</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">2</font> | <font style="color:rgba(6, 8, 31, 0.88);">预热</font> | <font style="color:rgba(6, 8, 31, 0.88);">刚启动时先低速允许，通过预热逐步上升</font> |


#### **<font style="color:rgba(6, 8, 31, 0.88);">默认行为：直接拒绝</font>**
<font style="color:rgba(6, 8, 31, 0.88);">若未特殊配置，Sentinel是</font>**<font style="color:rgba(6, 8, 31, 0.88);">直接丢弃（快速失败）</font>**<font style="color:rgba(6, 8, 31, 0.88);">超过阈值的请求，并返回“限流”异常（如BlockException），这样下游系统不会因为过量流量被压垮。</font>

#### **<font style="color:rgba(6, 8, 31, 0.88);">匀速排队模式（漏桶思想）</font>**
<font style="color:rgba(6, 8, 31, 0.88);">若选择该模式，超过阈值的请求会被短时间排队处理，超时再丢弃。</font>

#### **<font style="color:rgba(6, 8, 31, 0.88);">预热模式</font>**
<font style="color:rgba(6, 8, 31, 0.88);">常用于服务刚启动的流量缓升场景，避免冷启动雪崩。</font>

---

## <font style="color:rgba(6, 8, 31, 0.88);">三、如何配置与适用场景</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">通过</font>**<font style="color:rgba(6, 8, 31, 0.88);">流控规则</font>**<font style="color:rgba(6, 8, 31, 0.88);">（FlowRule）灵活选择对应策略，如：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">高频接口建议用直接拒绝（0），以免耗尽资源。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">部分容忍等待的请求可用匀速排队（1）。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">所有策略都能通过Sentinel Dashboard动态配置和变更。</font>

---

### **<font style="color:rgba(6, 8, 31, 0.88);">总结</font>**
+ <font style="color:rgba(6, 8, 31, 0.88);">Sentinel底层主要采用</font>**<font style="color:rgba(6, 8, 31, 0.88);">滑动窗口+计数器</font>**<font style="color:rgba(6, 8, 31, 0.88);">限流，部分场景引入令牌桶/漏桶思想。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">超过限流数的请求</font>**<font style="color:rgba(6, 8, 31, 0.88);">默认会被直接拒绝</font>**<font style="color:rgba(6, 8, 31, 0.88);">（丢弃）；可配置为排队或预热模式。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">具体选择哪种处理方式，要根据业务容忍度和实际需求配置。</font>



> 更新: 2025-05-05 19:31:51  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/kfaxscfgk5uasyg0>