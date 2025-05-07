# Java中止线程的三种方式

停止一个线程通常意味着在线程处理任务完成之前停掉正在做的操作，也就是放弃当前的操作。

在 Java 中有以下 3 种方法可以中止正在运行的线程：

1. 使用退出标志，使线程正常退出，也就是当 run() 方法完成后线程中止。
2. 使用 stop() 方法强行中止线程，但是不推荐使用这个方法，该方法已被弃用。
3. 使用 interrupt 方法中断线程。

## 1. 使用标志位中止线程（推荐）
在 run() 方法执行完毕后，该线程就中止了。但是在某些特殊的情况下，run() 方法会被一直执行；比如在服务端程序中可能会使用 `while(true) { ... }` 这样的循环结构来不断的接收来自客户端的请求。此时就可以用修改标志位的方式来结束 run() 方法。

```java
public class ExitFlagTests {
    // volatile修饰符用来保证其它线程读取的总是该变量的最新的值
    private volatile boolean exitFlag = false; // 退出标志

    public void run() {
        while (!exitFlag) {
            // 执行线程的任务
            System.out.println("Thread is running...");
            try {
                Thread.sleep(1000); // 模拟一些工作
            } catch (InterruptedException e) {
                // 处理中断（如果需要）
                Thread.currentThread().interrupt(); // 重新设置中断状态
            }
        }
        System.out.println("Thread is stopping...");
    }

    public void stop() {
        exitFlag = true; // 设置退出标志为true
    }

    public static void main(String[] args) throws InterruptedException {
        ExitFlagTests exit = new ExitFlagTests();
        Thread thread = new Thread(exit::run);
        thread.start();
        
        // 让线程运行一段时间
        Thread.sleep(5000);

        // 请求线程停止
        exit.stop();
    }
}
```

## 2. 使用 stop() 中止线程（不推荐）
`<font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Thread.stop()</font>`<font style="color:rgb(77, 77, 77);">方法可以强行中止线程的执行。然而，这种方法是不安全的，因为它不保证线程资源的正确释放和清理，可能导致数据不一致和资源泄露等问题，因此已被官方弃用。</font>

```java
public class StopMethodTests extends Thread {
    public void run() {
        while (true) {
            System.out.println("Thread is running...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        StopMethodTests thread = new StopMethodTests();
        thread.start();

        // 让线程运行一段时间
        Thread.sleep(5000);

        // 强行中止线程（不推荐）
        thread.stop();
    }
}

```

通过查看 JDK 的 API，我们会看到 java.lang.Thread 类型提供了一系列的方法如 start()、stop()、resume()、suspend()、destory()等方法来管理线程。但是除了 start() 之外，其它几个方法都被声名为已过时（deprecated）。

虽然 stop() 方法确实可以停止一个正在运行的线程，但是这个方法是不安全的，而且该方法已被弃用，最好不要使用它。  
JDK 文档中还引入用一篇文章来解释了弃用这些方法的原因：[《Why are Thread.stop, Thread.suspend and Thread.resume Deprecated?》](https://docs.oracle.com/javase/1.5.0/docs/guide/misc/threadPrimitiveDeprecation.html)

为什么弃用stop：

1. 调用 stop() 方法会立刻停止 run() 方法中剩余的全部工作，包括在 catch 或 finally 语句中的，并抛出ThreadDeath异常(通常情况下此异常不需要显示的捕获)，因此可能会导致一些清理性的工作的得不到完成，如文件，数据库等的关闭。
2. 调用 stop() 方法会立即释放该线程所持有的所有的锁，导致数据得不到同步，出现数据不一致的问题。

## 3. 使用 interrupt() 中断线程
现在我们知道了使用 stop() 方式停止线程是非常不安全的方式，那么我们应该使用什么方法来停止线程呢？答案就是使用 interrupt() 方法来**中断线程**。

需要明确的一点的是：interrupt() 方法并不像在 for 循环语句中使用 break 语句那样干脆，马上就停止循环。调用 interrupt() 方法仅仅是在当前线程中打一个停止的标记，并不是真的停止线程。

也就是说，线程中断并不会立即中止线程，而是通知目标线程，有人希望你中止。至于目标线程收到通知后会如何处理，则完全由目标线程自行决定。这一点很重要，如果中断后，线程立即无条件退出，那么我们又会遇到 stop() 方法的老问题。

事实上，如果一个线程不能被 interrupt，那么 stop 方法也不会起作用。

我们来看一个使用 interrupt() 的例子：

```java
public class InterruptTests extends Thread{
    public static void main(String[] args) {
        try {
            InterruptTests t = new InterruptTests();
            // 启动线程
            t.start();

            Thread.sleep(200);

            // 中断线程 t
            t.interrupt();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        super.run();
        // 循环打印 i 的值，从 0 到 200000
        for(int i = 0; i <= 200000; i++) {
            System.out.println("i=" + i);
        }
    }
}
```

```java
i=199993
i=199994
i=199995
i=199996
i=199997
i=199998
i=199999
i=200000
```

从输出的结果我们会发现 interrupt 方法并没有停止线程 t 中的处理逻辑，也就是说即使 t 线程被设置为了中断状态，但是这个中断并不会起作用，那么该如何停止线程呢？

这就需要使用到另外两个与线程中断有关的方法了：

```java
public boolean Thread.isInterrupted() //判断是否被中断
public static boolean Thread.interrupted() //判断是否被中断，并清除当前中断状态
```

这两个方法使得当前线程能够感知到是否被中断了（通过检查标志位）。

所以如果希望线程 t 在中断后停止，就必须先判断是否被中断，并为它增加相应的中断处理代码：

```java
@Override
public void run() {
    super.run();
    for(int i = 0; i <= 200000; i++) {
        //判断是否被中断
        if(Thread.currentThread().isInterrupted()){
            //处理中断逻辑
            break;
        }
        System.out.println("i=" + i);
    }
}
```

在上面这段代码中，我们增加了 Thread.isInterrupted() 来判断当前线程是否被中断了，如果是，则退出 for 循环，结束线程。

这种方式看起来与之前介绍的“使用标志位中止线程”非常类似，**但是在遇到 sleep() 或者 wait() 这样的操作，我们只能通过中断来处理了**。

`public static native void sleep(long millis) throws InterruptedException`

Thread.sleep() 方法会抛出一个 InterruptedException 异常，当线程被 sleep() 休眠时，如果被中断，这会就抛出这个异常。  
（注意：Thread.sleep() 方法由于中断而抛出的异常，是会清除中断标记的。）



> 更新: 2024-10-28 20:16:25  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/pf0y1xsbv1g3zb29>