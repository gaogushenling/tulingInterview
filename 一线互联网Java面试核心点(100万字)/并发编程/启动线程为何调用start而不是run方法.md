# 启动线程为何调用start而不是run方法

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">  
</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">调用 </font>****<font style="background-color:rgb(247, 247, 248);">start()</font>****<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 方法会告诉jvm创建一个新的线程</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，</font>并在这个新线程中执行与 run() 方法相关联的代码块。这个过程允许多个线程同时运行，每个线程都拥有独立的执行上下文，这意味着它们可以在不互相干扰的情况下执行任务。

与此不同，如果**直接调用 run() 方法，它仅仅是一个普通的方法调用，不会创建新的线程**。相反，它会在当前线程的上下文中执行 run() 方法中的代码。这将导致代码的顺序执行，没有并行性可言，因为它们都在同一个线程内执行。

这种区别非常重要，因为多线程编程的一个主要目标是实现并行性，从而提高程序的性能和响应能力。通过调用 start() 方法，可以利用多核处理器的优势，同时执行多个线程，以更有效地完成任务。而直接调用 run() 方法只是按照一般的方法顺序执行代码，无法发挥多线程的潜力。因此，使用 start() 方法来启动线程是实现并行性的关键。







> 更新: 2023-09-01 17:14:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/cb2gdq7s7dydefer>