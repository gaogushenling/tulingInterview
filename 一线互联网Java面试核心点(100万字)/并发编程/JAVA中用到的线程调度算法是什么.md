# JAVA 中用到的线程调度算法是什么

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">  
</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">在Java中，线程调度采用的是一种抢占式调度模型。</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这就像在一个抢夺战中，有较高优先级的线程将首先占用CPU资源。如果线程具有相同的优先级，那么Java虚拟机会随机选择一个线程来执行，以保持公平竞争的原则。一旦一个线程获得了CPU，它将一直运行，直到自愿放弃CPU资源，或者由于某些情况（比如等待I/O操作、等待锁等）被迫放弃CPU，从而让其他线程有机会执行。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">这种抢占式调度模型的目标是确保具有较高优先级的线程在争夺CPU资源时具有优势，但仍然让较低优先级的线程有机会执行，以避免它们被永远地忽略。Java的多线程调度机制有助于平衡线程之间的竞争和公平性，从而提高了多线程程序的响应速度和效率。</font>



> 更新: 2023-09-01 16:41:21  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/cc9qdabbp7bzsp9f>