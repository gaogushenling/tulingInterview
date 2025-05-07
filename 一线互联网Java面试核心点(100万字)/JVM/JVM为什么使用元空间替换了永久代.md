# JVM为什么使用元空间替换了永久代

<font style="color:rgba(0, 0, 0, 0.82);">在 Java 8 中，JVM 对内存管理进行了一个重要的变化，就是用元空间（Metaspace）替代了永久代（PermGen）。这个变更解决了一些持久性的问题，并提供了更多的灵活性和稳定性。</font>

<font style="color:rgba(0, 0, 0, 0.82);">以下是为什么 JVM 从永久代转向元空间的几个关键原因：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 内存限制</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">永久代限制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：永久代在 JVM 中存在一个固定的内存大小限制，通常是通过 JVM 参数</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">-XX:PermSize</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">-XX:MaxPermSize</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">设置的。但在实际应用中，预测应用程序所需的永久代大小并不容易，且其固定大小可能会导致</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">OutOfMemoryError: PermGen space</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">错误。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">元空间优势</font>**<font style="color:rgba(0, 0, 0, 0.82);">：元空间不是堆的一部分，存放在本机内存（Native Memory）中，不受 JVM 堆大小限制，因此更能有效地利用内存资源。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. 类和类加载器的生命周期管理</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">永久代问题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：由于类及其元数据由永久代持有，因此类加载器和类的动态卸载过程会受到永久代容量的约束。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">元空间解决</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用元空间减少了对应用程序行为的依赖性，实现更好地管理类及其元数据的内存分配，使类加载器更可靠地执行类的动态卸载。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">3. 性能和优化</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能问题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在多环境或不同规模的应用程序中，优化和调优永生代（尤其是调整大小）比较复杂，并可能由于设置不当影响性能。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">优化改进</font>**<font style="color:rgba(0, 0, 0, 0.82);">：元空间利用操作系统的内存进行类元数据信息的存储，这使得 JVM 在启动阶段和内存管理上表现更为高效。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">4. 减少 Full GC 问题</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">Full GC：</font>**<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">永久代的垃圾回收会触发 Full GC，这是非常耗时的过程，在高负载系统中影响较大。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">消除阻塞</font>**<font style="color:rgba(0, 0, 0, 0.82);">：元空间独立于堆内存，大大减少了永久代相关的 Full GC 次数，因此在运行时减少了长时间的中断。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">5. 更好的监控和调优</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">监控复杂性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：永久代的大小不易调整和监控。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">增强监控工具</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过元空间，可以更容易使用现代工具（如 jcmd 和 jconsole）来监控和调优 Java 应用的内存使用情况。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">引入元空间的设计变化让虚拟机层面对类元数据的存储和管理更有效率、灵活性更强，同时降低调试和运维复杂度，提升了 JVM 的性能和稳定性。这使得开发人员不再需要担心 PermGen 的调优及相应的内存管理问题，能更专注于应用的业务逻辑开发。</font>



> 更新: 2024-08-09 16:05:49  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/wkzrrp97dyetxr0v>