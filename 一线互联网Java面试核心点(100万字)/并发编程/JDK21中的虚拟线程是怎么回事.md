# JDK 21中的虚拟线程是怎么回事

<font style="color:rgba(0, 0, 0, 0.82);">JDK 21引入了一个重要的新特性：虚拟线程（Virtual Threads）。虚拟线程是Java的项目Loom的一部分，旨在简化并发编程并提高Java应用程序的可扩展性。以下是关于虚拟线程的一些关键细节：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">什么是虚拟线程？</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">轻量级线程</font>**<font style="color:rgba(0, 0, 0, 0.82);">：虚拟线程是轻量级的用户模式线程，它们与传统的Java平台线程（也称为内核线程）不同。虚拟线程在JVM中运行，而不是直接映射到操作系统的内核线程。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">调度和管理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：虚拟线程由JVM调度和管理，而不是依赖于操作系统提供的调度机制。这使得虚拟线程的创建和切换成本非常低。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">并发模型简化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：因为虚拟线程极其轻量，因此可以创建大量线程，而不需要担心原有平台线程的高开销。这使得以“每个请求一个线程”来处理并发任务变得可行，简化了编程模型。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">优势</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">高可扩展性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以创建大量的虚拟线程，数百万级别的线程创建和销毁是可行的，从而更好地利用多核处理器的能力。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">简化代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：程序员无需使用复杂的非阻塞编程手法（如回调和Future）来实现并发和并行代码，因为虚拟线程使得以同步风格编写并发程序成为可能。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">减少阻塞问题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在传统线程中，一个线程阻塞会浪费系统资源，而虚拟线程在阻塞时会自动切换到其他正在运行的线程，利用时间片效率。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用虚拟线程，您可以像使用普通线程一样，简单地启动和操作它们。以下是一个简单的代码示例：</font>

```java
public class VirtualThreadExample {  
    public static void main(String[] args) throws InterruptedException {  
        Thread virtualThread = Thread.ofVirtual().start(() -> {  
            System.out.println("Hello from virtual thread: " + Thread.currentThread());  
        });  

        virtualThread.join(); // 等待虚拟线程完成  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">创建虚拟线程</font>
<font style="color:rgba(0, 0, 0, 0.82);">虚拟线程可以通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">Thread.ofVirtual().start()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法启动，API的设计使得使用虚拟线程与现有线程API保持一致，降低了学习成本。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">适用场景</font>
<font style="color:rgba(0, 0, 0, 0.82);">虚拟线程特别适合IO密集型任务或需要大量并发执行的任务，例如服务器编程场景中处理大量请求，或使用阻塞IO操作的应用程序。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">对现有代码的影响</font>
<font style="color:rgba(0, 0, 0, 0.82);">虚拟线程的引入不会影响现有的应用程序，因为它们增加了一种并发操作的选择，而不会移除或替换现有的功能。同时，由于它们是轻量级的，迁移到使用虚拟线程通常只需要很少的代码修改。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">注意事项</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">性能监控</font>**<font style="color:rgba(0, 0, 0, 0.82);">：虽然虚拟线程相对于传统线程更轻量，但在监控和调试时仍需要注意它们的生命周期，因为它们可能会大规模地被创建和销毁。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">库支持</font>**<font style="color:rgba(0, 0, 0, 0.82);">：大部分现有的Java库在使用虚拟线程时无需修改，不过某些需要深度与操作系统线程交互的库可能需要检查兼容性。</font>

<font style="color:rgba(0, 0, 0, 0.82);">虚拟线程为Java引入了一种新的并发编程模型，使编写高并发Java应用程序变得更直观和简单，它极大地提升了Java程序的可扩展性和可维护性。</font>



> 更新: 2024-08-11 19:08:03  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/kbt0anw9ro4a0qt2>