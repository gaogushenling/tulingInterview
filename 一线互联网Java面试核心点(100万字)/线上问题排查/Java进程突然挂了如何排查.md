# Java进程突然挂了如何排查

<font style="color:rgba(0, 0, 0, 0.82);">当Java进程突然挂掉时，可能是由于程序崩溃、资源限制、外部干扰等多种因素导致的。排查这些问题需要全面的分析，从日志、线程转储到系统资源监控。以下是一个详细的排查过程和步骤。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">排查步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">日志分析</font>**
2. **<font style="color:rgba(0, 0, 0, 0.82);">生成和分析Heap Dump</font>**
3. **<font style="color:rgba(0, 0, 0, 0.82);">检查系统资源</font>**
4. **<font style="color:rgba(0, 0, 0, 0.82);">分析代码和依赖</font>**
5. **<font style="color:rgba(0, 0, 0, 0.82);">环境配置检查</font>**
6. **<font style="color:rgba(0, 0, 0, 0.82);">监控和验证</font>**

### <font style="color:rgba(0, 0, 0, 0.82);">详细的排查过程</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. 日志分析</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">应用日志</font>**<font style="color:rgba(0, 0, 0, 0.82);">：查看应用程序日志（例如</font>`<font style="color:rgba(0, 0, 0, 0.82);">application.log</font>`<font style="color:rgba(0, 0, 0, 0.82);">），搜索异常、错误信息和未处理的异常。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">系统日志</font>**<font style="color:rgba(0, 0, 0, 0.82);">：检查操作系统日志（如</font>`<font style="color:rgba(0, 0, 0, 0.82);">/var/log/syslog</font>`<font style="color:rgba(0, 0, 0, 0.82);">或</font>`<font style="color:rgba(0, 0, 0, 0.82);">/var/log/messages</font>`<font style="color:rgba(0, 0, 0, 0.82);">）是否有突发事件记录。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">关键日志条目</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ `<font style="color:rgba(0, 0, 0, 0.82);">java.lang.OutOfMemoryError</font>`<font style="color:rgba(0, 0, 0, 0.82);">：内存不足。</font>
+ `<font style="color:rgba(0, 0, 0, 0.82);">java.lang.StackOverflowError</font>`<font style="color:rgba(0, 0, 0, 0.82);">：栈溢出。</font>
+ `<font style="color:rgba(0, 0, 0, 0.82);">java.lang.NoClassDefFoundError</font>`<font style="color:rgba(0, 0, 0, 0.82);">：类未找到。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 生成和分析Heap Dump</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">在应用启动时设置JVM参数，在崩溃时生成Heap Dump：</font>

```java
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dump
```

+ **<font style="color:rgba(0, 0, 0, 0.82);">分析工具</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用Memory Analyzer Tool（MAT）分析Heap Dump，识别内存泄漏和占用最多内存的对象。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">3. 检查系统资源</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">CPU使用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">top</font>`<font style="color:rgba(0, 0, 0, 0.82);">或</font>`<font style="color:rgba(0, 0, 0, 0.82);">htop</font>`<font style="color:rgba(0, 0, 0, 0.82);">监控CPU负载，查看是否有异常的负载尖峰。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">内存使用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">free -m</font>`<font style="color:rgba(0, 0, 0, 0.82);">或</font>`<font style="color:rgba(0, 0, 0, 0.82);">vmstat</font>`<font style="color:rgba(0, 0, 0, 0.82);">检查内存使用情况。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">确认系统是否有过度的内存消耗。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">磁盘空间</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">df -h</font>`<font style="color:rgba(0, 0, 0, 0.82);">检查磁盘空间，确认是否有磁盘写满的情况。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">4. 分析代码和依赖</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">代码审查</font>**<font style="color:rgba(0, 0, 0, 0.82);">：检查最近的代码变更是否引入了错误。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">外部库</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确定是否使用了不兼容或有漏洞的第三方库。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">常见问题场景</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">未关闭的文件句柄或数据库连接。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">死锁或其他并发问题导致的资源占用。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">5. 环境配置检查</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">JVM参数</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确认启动参数是否合适，例如</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xmx</font>`<font style="color:rgba(0, 0, 0, 0.82);">（最大堆内存大小）和</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xms</font>`<font style="color:rgba(0, 0, 0, 0.82);">（初始堆内存大小）的设置。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">环境依赖</font>**<font style="color:rgba(0, 0, 0, 0.82);">：检查Java版本、操作系统版本及补丁更新等。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">6. 监控和验证</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">监控加强</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用Prometheus、Grafana等工具对系统和应用进行实时监控。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">设置合适的报警阈值，在出现异常情况时及时获得通知。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">验证修复</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">针对识别问题进行修复后，在接近生产环境的测试环境中进行验证。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通过自动化测试覆盖关键功能和场景。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">具体示例</font>
**<font style="color:rgba(0, 0, 0, 0.82);">问题描述</font>**<font style="color:rgba(0, 0, 0, 0.82);">：一个Java Web应用在运行一段时间后突然挂掉。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">排查过程</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">日志分析</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">查看应用日志，发现出现</font>`<font style="color:rgba(0, 0, 0, 0.82);">java.lang.StackOverflowError</font>`<font style="color:rgba(0, 0, 0, 0.82);">错误和反复递归调用。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">系统日志中没有发现内存溢出记录，但有些CPU使用率高的记录。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">生成和分析Heap Dump</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">配置了</font>`<font style="color:rgba(0, 0, 0, 0.82);">-XX:+HeapDumpOnOutOfMemoryError</font>`<font style="color:rgba(0, 0, 0, 0.82);">，未找到堆转储，排除内存不足。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">检查系统资源</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">top</font>`<font style="color:rgba(0, 0, 0, 0.82);">检查时发现进程占用了100% CPU，疑似陷入死循环。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">分析代码和依赖</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">发现递归函数中缺乏退出条件，导致无穷递归，从而导致栈溢出。</font>

```java
public int computeFactorial(int n) {  
// 缺少 n == 0 的退出条件  
return n * computeFactorial(n - 1);  
}
```

**<font style="color:rgba(0, 0, 0, 0.82);">修复</font>**<font style="color:rgba(0, 0, 0, 0.82);">：添加递归基准条件。</font>

```java
public int computeFactorial(int n) {  
if (n == 0) return 1;  
return n * computeFactorial(n - 1);  
}
```

5. **<font style="color:rgba(0, 0, 0, 0.82);">环境配置检查</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">确保JVM的内存参数</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xms4g -Xmx4g</font>`<font style="color:rgba(0, 0, 0, 0.82);">符合资源需求。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">监控和验证</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在测试环境中通过负载测试验证修复效果。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">增强Grafana监控和Prometheus报警机制对关键性能指标的实时监控。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">Java进程的突然挂掉可能由于多种原因，包括代码错误、资源限制、环境配置或外部依赖问题。通过全面的检查和分析，可以逐步识别和解决问题。使用现代化的监控和报警工具也可帮助提前预警和排查生产环境中潜在的问题。</font>



> 更新: 2024-08-26 14:53:18  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/cki480fe533m84ng>