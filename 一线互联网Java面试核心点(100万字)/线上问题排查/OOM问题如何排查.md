# OOM问题如何排查

<font style="color:rgba(0, 0, 0, 0.82);">当Java应用程序遇到OOM（OutOfMemoryError）错误时，通常意味着程序超出了可用的内存容量。这可能由于内存泄漏或配置不足导致。以下是详细的排查过程和示例，帮助诊断和解决这种问题。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">排查步骤</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. 确认OOM错误类型</font>
<font style="color:rgba(0, 0, 0, 0.82);">首先，需要明确是哪种OOM：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">java.lang.OutOfMemoryError: Java heap space</font>**<font style="color:rgba(0, 0, 0, 0.82);">：堆内存不足。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">java.lang.OutOfMemoryError: Metaspace</font>**<font style="color:rgba(0, 0, 0, 0.82);">：元空间不足（JDK 8及以上）。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">java.lang.OutOfMemoryError: GC overhead limit exceeded</font>**<font style="color:rgba(0, 0, 0, 0.82);">：GC时间过长。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 收集诊断信息</font>
<font style="color:rgba(0, 0, 0, 0.82);">收集以下信息：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">Error日志</font>**<font style="color:rgba(0, 0, 0, 0.82);">：找到OOM发生时的日志片段。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">Heap dump</font>**<font style="color:rgba(0, 0, 0, 0.82);">：获取堆转储文件（可以使用-XX:+HeapDumpOnOutOfMemoryError来自动生成）。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">GC日志</font>**<font style="color:rgba(0, 0, 0, 0.82);">：启用GC日志以分析垃圾回收活动。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例JVM参数</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dump -Xlog:gc*:file=gc.log:time
```

#### <font style="color:rgba(0, 0, 0, 0.82);">3. 分析Heap Dump</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用工具分析堆转储文件，找出内存泄漏的来源或占用最多空间的对象。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">工具</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Eclipse Memory Analyzer (MAT)</font>

**<font style="color:rgba(0, 0, 0, 0.82);">分析步骤</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">加载Heap Dump</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用MAT打开生成的堆转储文件。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">查找大对象</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用MAT的“Histogram”视图查看占用内存最大的对象类型。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">查找泄漏疑点</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用“Leak Suspects Report”功能自动分析可能的内存泄漏点。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例分析</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">通过MAT发现</font>`<font style="color:rgba(0, 0, 0, 0.82);">java.util.ArrayList</font>`<font style="color:rgba(0, 0, 0, 0.82);">实例占用了大量的内存，并继续深入查看引用路径。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">4. 分析和识别问题代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">检查代码中可能导致OOM的地方。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">大对象集合</font>**<font style="color:rgba(0, 0, 0, 0.82);">：导致内存占用过大的大型集合（如List, Map等）。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">长生命周期对象</font>**<font style="color:rgba(0, 0, 0, 0.82);">：一些不必要的对象拥有比预期更长的生命周期。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">常见问题场景</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">缓存未清理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用自定义缓存，没有合适的过期或清理机制。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">无限增长的数据结构</font>**<font style="color:rgba(0, 0, 0, 0.82);">：例如，把无限的请求数据储存在集合中。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">5. 优化代码和配置</font>
**<font style="color:rgba(0, 0, 0, 0.82);">解决方法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">优化数据结构</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用合适的数据结构和集合类。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">清理长生命周期对象</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确保不再需要的对象能够被GC及时回收。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">调整JVM参数</font>**<font style="color:rgba(0, 0, 0, 0.82);">：如果应用确实需要更多内存，适当调整堆大小。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码修复</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">假设原代码存在问题：</font>

```java
List<String> largeList = new ArrayList<>();  

public void processData(List<String> data) {  
    largeList.addAll(data); // 增长无限制  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">修复后：</font>

```java
private static final int MAX_SIZE = 10000;  

public void processData(List<String> data) {  
    if (largeList.size() + data.size() > MAX_SIZE) {  
        largeList.clear(); // 清理或采取措施避免无限增长  
    }  
    largeList.addAll(data);  
}
```

**<font style="color:rgba(0, 0, 0, 0.82);">配置调优</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">增加堆大小以适应应用的需要（如果合理）。</font>

```java
-Xmx4g -Xms2g
```

#### <font style="color:rgba(0, 0, 0, 0.82);">6. 验证和监控</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">运行负载测试验证修改后的程序性能和内存使用。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">持续进行内存使用监控，避免再次出现OOM。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例全面讲解</font>
**<font style="color:rgba(0, 0, 0, 0.82);">场景</font>**<font style="color:rgba(0, 0, 0, 0.82);">：一个Java Web应用程序在高负载测试中抛出</font>`<font style="color:rgba(0, 0, 0, 0.82);">java.lang.OutOfMemoryError: Java heap space</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">步骤回顾</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">配置JVM参数</font>**<font style="color:rgba(0, 0, 0, 0.82);">以捕捉OOM：</font>

```java
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dump -Xlog:gc*:file=gc.log:time
```

2. **<font style="color:rgba(0, 0, 0, 0.82);">收集Heap Dump</font>**<font style="color:rgba(0, 0, 0, 0.82);">在OOM发生时的转储文件。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">使用MAT分析</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">打开堆转储，查看</font>`<font style="color:rgba(0, 0, 0, 0.82);">Histogram</font>`<font style="color:rgba(0, 0, 0, 0.82);">，发现</font>`<font style="color:rgba(0, 0, 0, 0.82);">Session</font>`<font style="color:rgba(0, 0, 0, 0.82);">对象占用了大量内存。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">Dominator Tree</font>`<font style="color:rgba(0, 0, 0, 0.82);">分析，发现</font>`<font style="color:rgba(0, 0, 0, 0.82);">Session</font>`<font style="color:rgba(0, 0, 0, 0.82);">没有及时失效。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">问题识别</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">应用存在一个自定义</font>`<font style="color:rgba(0, 0, 0, 0.82);">Session</font>`<font style="color:rgba(0, 0, 0, 0.82);">管理模块，未能正确地释放过期的Session。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">永不过期的Session导致内存耗尽。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">代码优化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">实现Session超时机制。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在每次请求后或固定的时间间隔清理过期的Session。</font>

```java
public void cleanupSessions() {  
    sessions.entrySet().removeIf(entry -> entry.getValue().isExpired());  
}
```

1. **<font style="color:rgba(0, 0, 0, 0.82);">配置调整与测试</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">增加堆内存配置（如从</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xmx512m</font>`<font style="color:rgba(0, 0, 0, 0.82);">增加到</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xmx1g</font>`<font style="color:rgba(0, 0, 0, 0.82);">）。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">执行负载测试，验证修复效果。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">监控和告警</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">配置实时监控内存使用情况的工具。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">设置内存使用率告警，防止问题重现。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过系统化地分析和优化内存管理，应用程序在高负载下运行稳定，未再出现内存溢出错误。此例展示了如何排查OOM问题，结合工具和编程实践，有效解决实际中的复杂问题。</font>



> 更新: 2024-08-25 22:26:59  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/bgpy80cw6x7fntek>