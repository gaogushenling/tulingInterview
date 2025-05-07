# JAVA 守护线程和本地线程的区别

**<font style="background-color:rgb(247, 247, 248);">守护线程</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，可以看作是为其他线程提供服务的辅助工具。它们通常执行一些后台任务，比如垃圾回收、定期检查、日志记录等。一个重要的特点是它们不会阻止JVM的退出。当所有的本地线程都执行完毕后，JVM会自动退出，不会等待守护线程。</font>

**<font style="background-color:rgb(247, 247, 248);">本地线程</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，则是应用程序的主力军，执行着应用的核心逻辑。它们的存在会阻止JVM退出，因为只要还有本地线程在运行，JVM会继续工作。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">至于如何创建它们，守护线程需要通过设置 </font>**<font style="background-color:rgb(247, 247, 248);">setDaemon(true)</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 来告诉JVM它是守护线程。而本地线程则是默认的线程类型，不需要额外设置。</font>



> 更新: 2023-09-01 13:51:35  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/sxh84cgxodzcys5r>