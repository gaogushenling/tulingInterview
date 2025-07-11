# 你是如何排查线上OOM问题的

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">排查线上OOM问题是一个复杂的过程，需要综合运维经验、日志分析和性能监控等多个方面的信息。下面是一般的排查步骤：</font>

1. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">收集信息</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：首先，收集与OOM问题相关的信息，包括</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">错误日志</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">、</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">堆转储文件</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">、</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">线程转储文件</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">等。这些信息可以帮助我们了解OOM问题发生的上下文和可能的原因。</font>
2. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">分析堆转储文件</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：使用工具（如MAT、VisualVM等）分析堆转储文件，查看内存使用情况、对象分布和可能的内存泄漏等。通过分析对象的引用关系，可以确定是否存在内存泄漏问题。</font>
3. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">分析线程转储文件</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：使用工具（如jstack、VisualVM等）分析线程转储文件，查看线程状态、死锁情况和可能的死锁原因。通过分析线程的堆栈信息，可以确定是否存在死锁或线程阻塞等问题。</font>
4. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">检查代码</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：检查应用程序的代码，查找可能导致</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">内存泄漏</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">或</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">内存占用过高</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">的地方。特别关注对大对象的创建和持有、缓存使用不当、资源未释放等问题。</font>
5. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">监控系统资源</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：使用性能监控工具（如JMX、Grafana等）监控系统的CPU、内存、磁盘和网络等资源使用情况。查看系统的负载情况，是否存在资源瓶颈或异常情况。</font>
6. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">优化配置和调整参数</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：根据分析结果，优化应用程序的配置和调整相关的参数。例如，调整堆内存大小、线程池大小、GC策略等，以提高系统的性能和稳定性。</font>
7. **<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">预防措施</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">：根据排查结果，制定预防措施，避免类似的OOM问题再次发生。例如，改进代码质量、加强内存管理、优化资源使用等。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">需要注意的是，排查OOM问题需要综合运维经验和技术知识，有时需要借助专业的工具和专家的帮助。同时，也要注意及时备份和保留相关的日志和转储文件，以便后续的分析和排查。</font>



> 更新: 2023-08-28 22:42:41  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/etd8gymgwyli8xnw>