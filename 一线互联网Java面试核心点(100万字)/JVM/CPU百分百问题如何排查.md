# CPU百分百问题如何排查

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">排查CPU百分百问题通常需要一步一步地识别并解决潜在的原因。以下是一些常见的排查步骤：</font>

1. **<font style="background-color:rgb(247, 247, 248);">查看系统负载</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：首先，使用系统监控工具比如top查看系统的负载情况。</font>
2. **<font style="background-color:rgb(247, 247, 248);">确定是哪个进程导致CPU高占用</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：查找哪个进程或应用程序的CPU占用率很高。通常，系统监控工具会列出占用CPU较多的进程。注意，有时一个进程的子进程也可能引起CPU高占用。</font>
3. **<font style="background-color:rgb(247, 247, 248);">查看日志文件</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：检查应用程序的日志文件，查找是否有异常或错误消息。</font>
4. **<font style="background-color:rgb(247, 247, 248);">检查代码</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：如果是自己开发的应用程序，检查代码以查找是否存在性能问题，例如死循环、低效的算法、内存泄漏等。使用性能分析工具来帮助确定瓶颈。</font>
5. **<font style="background-color:rgb(247, 247, 248);">查看数据库查询</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：如果应用程序与数据库交互，查询可能导致CPU负载高。通过检查数据库的慢查询日志和优化查询来解决问题。</font>
6. **<font style="background-color:rgb(247, 247, 248);">监控线程</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：如果是多线程应用程序，检查是否有某些线程占用了大量CPU资源。使用线程分析工具来识别问题线程。</font>
7. **<font style="background-color:rgb(247, 247, 248);">查看网络连接</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：有时，网络请求和连接问题也可能导致CPU高占用。查看是否有异常的网络连接或请求。</font>
8. **<font style="background-color:rgb(247, 247, 248);">使用性能分析工具</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：使用专业的性能分析工具来检测瓶颈。例如，Java应用程序可以使用Arthas、VisualVM等工具进行分析。</font>
9. **<font style="background-color:rgb(247, 247, 248);">应用程序优化</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：根据排查的结果，对应用程序进行优化，修复性能问题。</font>



> 更新: 2023-09-04 11:25:38  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dc6f35grnp5rooqd>