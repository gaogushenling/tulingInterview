# OOM一定会导致JVM退出吗

<font style="color:rgba(0, 0, 0, 0.82);">在Java中，发生了OutOfMemoryError（OOM）不一定会导致整个JVM退出。是否退出取决于发生OOM错误的线程和错误处理逻辑。这是一个复杂的问题，具体行为会因应用程序实现方式、错误发生的情境以及错误处理策略而异。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">情况分析</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">主线程中未处理的OOM</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果在主线程中发生OOM且没有被捕获，JVM通常会终止程序并退出。这是因为JVM中没有其他存活的非守护线程来保持程序运行。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">子线程中未处理的OOM</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在非主线程中，如果OOM发生且未被捕获，该线程会停止执行。但如果其他非守护线程仍在运行，JVM不会退出。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">捕获并处理OOM</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果在代码中捕获并正确处理了OOM错误，JVM则可以继续执行其余的程序代码。合适的错误处理可能包括释放内存资源或提示用户进行适当的操作。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下是一个简单的示例，展示了在不同线程中处理OOM的行为差异：</font>

```java
public class OOMExample {  
    public static void main(String[] args) throws InterruptedException{  
        // 子线程中发生OOM并及时处理
        Thread thread1 = new Thread(() -> {
            try {
                oomMethod();
            } catch (OutOfMemoryError e) {
                System.out.println("Handled OOM in thread. JVM will not exit.");
            }
        });

        // 子线程中发生OOM不处理
        Thread thread2 = new Thread(() -> {
            oomMethod();
        });

        thread1.start();
        Thread.sleep(3000);

        thread2.start();
        Thread.sleep(3000);
        // 主线程中发生OOM并且及时处理，JVM不会退出
        try {
            oomMethod();
        } catch (OutOfMemoryError e) {
            System.out.println("Handled OOM in main. JVM will continue.");
        }

        // 主线程中发生OOM而未处理,JVM会马上退出
        System.out.println("JVM退出前");
        oomMethod();
        System.out.println("JVM退出后");
    }  

    public static void oomMethod() {  
        int[] array = new int[Integer.MAX_VALUE]; // 试图分配过大的数组，触发OOM  
    }  
}

输出结果：
Handled OOM in thread. JVM will not exit.
Exception in thread "Thread-1" java.lang.OutOfMemoryError: Requested array size exceeds VM limit
	at com.zhuge.controller.StockController.oomMethod(StockController.java:120)
	at com.zhuge.controller.StockController.lambda$main$1(StockController.java:98)
	at com.zhuge.controller.StockController$$Lambda$2/648129364.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:745)
Handled OOM in main. JVM will continue.
JVM退出前
Exception in thread "main" java.lang.OutOfMemoryError: Requested array size exceeds VM limit
	at com.zhuge.controller.StockController.oomMethod(StockController.java:120)
	at com.zhuge.controller.StockController.main(StockController.java:115)
```

### <font style="color:rgba(0, 0, 0, 0.82);">行为解释</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">在主线程中，如果未捕获的OOM发生，程序将立即终止。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">在线程</font>`<font style="color:rgba(0, 0, 0, 0.82);">thread</font>`<font style="color:rgba(0, 0, 0, 0.82);">中，我们捕获了OOM并进行了打印处理。即使发生了OOM，该子线程停止，但主线程继续执行主线程外的任务。如果没有捕获，子线程停止，但JVM不会退出，因为主线程仍在运行。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">此示例代码通过捕获异常展示了如何使程序在发生OOM时继续执行，但开发者应合理处理这些错误以避免不必要的错误传播和程序行为失常。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">注意事项</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">不建议频繁捕获OOM并继续执行程序，因为这样可能表明程序有严重的内存管理问题，应尽量优化内存使用。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">在关键路径中发生OOM时，通常应记录日志并考虑安全停机，因为无法保证系统在内存压力下的正确性。</font>



> 更新: 2024-08-11 15:03:50  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ruez0eorf445inye>