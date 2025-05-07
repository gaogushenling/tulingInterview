# 频繁FullGC问题如何排查

<font style="color:rgba(0, 0, 0, 0.82);">频繁的Full GC（完全垃圾收集）通常表明应用程序在内存管理方面存在问题，可能导致性能下降。以下是排查步骤和一个详细的示例：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">排查步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">收集GC日志</font>**
2. **<font style="color:rgba(0, 0, 0, 0.82);">分析GC日志</font>**
3. **<font style="color:rgba(0, 0, 0, 0.82);">监控JVM内存使用情况</font>**
4. **<font style="color:rgba(0, 0, 0, 0.82);">分析堆内存</font>**
5. **<font style="color:rgba(0, 0, 0, 0.82);">检查代码中的内存使用</font>**
6. **<font style="color:rgba(0, 0, 0, 0.82);">调整JVM参数</font>**
7. **<font style="color:rgba(0, 0, 0, 0.82);">优化代码</font>**
8. **<font style="color:rgba(0, 0, 0, 0.82);">验证改进</font>**

### <font style="color:rgba(0, 0, 0, 0.82);">详细示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">假设我们有一个大型电子商务网站，最近用户反馈系统响应变慢。运维团队发现服务器频繁出现Full GC，严重影响性能。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">1. 收集GC日志</font>
<font style="color:rgba(0, 0, 0, 0.82);">首先，我们需要开启详细的GC日志。在JVM参数中添加：</font>

```java
-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/path/to/gc.log
```

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 分析GC日志</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用工具（如GCViewer）分析GC日志，我们发现：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">Full GC频率：每10分钟一次</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">每次Full GC耗时：平均3秒</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Old Gen使用情况：每次GC后仍然保持在80%以上</font>

<font style="color:rgba(0, 0, 0, 0.82);">GC日志片段示例：</font>

```java
2023-05-15T14:30:45.123+0800: [Full GC (Ergonomics) [PSYoungGen: 20M->0M(60M)] [ParOldGen: 180M->175M(200M)] 200M->175M(260M), [Metaspace: 30M->30M(1024M)], 3.2345678 secs] [Times: user=10.23 sys=0.25, real=3.23 secs]
```

#### <font style="color:rgba(0, 0, 0, 0.82);">3. 监控JVM内存使用情况</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用工具如VisualVM或JConsole实时监控JVM内存使用。我们观察到：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">Eden区：频繁被填满后触发Minor GC</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Survivor区：经常接近满载</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Old Gen：持续增长，即使在Full GC后也难以下降</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">4. 分析堆内存</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用jmap生成堆转储文件：</font>

```java
jmap -dump:format=b,file=heap_dump.hprof <pid>
```

<font style="color:rgba(0, 0, 0, 0.82);">使用MAT（Memory Analyzer Tool）分析堆转储，发现：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">大量的</font>`<font style="color:rgba(0, 0, 0, 0.82);">com.example.Order</font>`<font style="color:rgba(0, 0, 0, 0.82);">对象占用了Old Gen的大部分空间</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">这些</font>`<font style="color:rgba(0, 0, 0, 0.82);">Order</font>`<font style="color:rgba(0, 0, 0, 0.82);">对象中包含了大量历史订单数据</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">5. 检查代码中的内存使用</font>
<font style="color:rgba(0, 0, 0, 0.82);">审查相关代码，发现问题：</font>

```java
public class OrderService {  
    private static final List<Order> allOrders = new ArrayList<>();  

    public void processOrder(Order order) {  
        // 处理订单  
        allOrders.add(order);  // 问题所在：持续添加订单到静态列表  
    }  

    // 其他方法...  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">6. 调整JVM参数</font>
<font style="color:rgba(0, 0, 0, 0.82);">临时调整JVM参数以缓解问题：</font>

```java
-Xms4g -Xmx4g -XX:NewRatio=2 -XX:SurvivorRatio=8
```

#### <font style="color:rgba(0, 0, 0, 0.82);">7. 优化代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">修改代码以解决根本问题：</font>

```java
public class OrderService {  
    private static final int MAX_ORDERS = 10000;  
    private static final Queue<Order> recentOrders = new LinkedList<>();  

    public void processOrder(Order order) {  
        // 处理订单  
        recentOrders.offer(order);  
        if (recentOrders.size() > MAX_ORDERS) {  
            recentOrders.poll();  // 移除最旧的订单  
        }  
    }  

    // 其他方法...  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">同时，实现一个定时任务将处理过的订单数据持久化到数据库，并从内存中清除。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">8. 验证改进</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">重新部署优化后的应用</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">监控GC活动和内存使用</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">进行负载测试</font>

<font style="color:rgba(0, 0, 0, 0.82);">结果：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">Full GC频率降低到每小时1-2次</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">每次Full GC耗时减少到1秒以内</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Old Gen使用率稳定在60%左右</font>

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">通过这个详细的排查过程，我们：</font>

1. <font style="color:rgba(0, 0, 0, 0.82);">使用GC日志和监控工具识别了问题</font>
2. <font style="color:rgba(0, 0, 0, 0.82);">通过堆内存分析找到了内存泄漏的根源</font>
3. <font style="color:rgba(0, 0, 0, 0.82);">优化了代码中的内存使用模式</font>
4. <font style="color:rgba(0, 0, 0, 0.82);">调整了JVM参数以更好地适应应用特性</font>
5. <font style="color:rgba(0, 0, 0, 0.82);">验证了优化效果</font>

<font style="color:rgba(0, 0, 0, 0.82);">这个例子展示了如何系统地排查和解决频繁Full GC问题，涵盖了从问题发现、原因分析到解决方案实施的完整过程。在实际工作中，具体的问题可能更加复杂，但这个方法论可以作为一个基础框架来处理各种GC相关的性能问题。</font>



> 更新: 2024-08-23 21:57:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/itakbe1872na814s>