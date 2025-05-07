# Java死锁问题如何排查

<font style="color:rgba(0, 0, 0, 0.82);">在Java应用程序中，死锁是一个经典的并发问题，它会导致线程永久阻塞，无法继续进行任何操作。排查Java死锁问题需要熟悉多线程调试技巧和工具。以下是一个详细的排查过程以及一个具体的示例。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">排查步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">识别死锁现象</font>**
2. **<font style="color:rgba(0, 0, 0, 0.82);">收集线程转储</font>**
3. **<font style="color:rgba(0, 0, 0, 0.82);">分析线程转储</font>**
4. **<font style="color:rgba(0, 0, 0, 0.82);">代码审查</font>**
5. **<font style="color:rgba(0, 0, 0, 0.82);">重现问题</font>**
6. **<font style="color:rgba(0, 0, 0, 0.82);">使用调试工具</font>**
7. **<font style="color:rgba(0, 0, 0, 0.82);">优化和验证</font>**

### <font style="color:rgba(0, 0, 0, 0.82);">详细排查过程</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. 识别死锁现象</font>
<font style="color:rgba(0, 0, 0, 0.82);">通常，死锁会表现为应用程序挂起、不响应用户请求或CPU使用率下降。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 收集线程转储</font>
<font style="color:rgba(0, 0, 0, 0.82);">当应用出现不响应时，使用以下方法收集Java线程转储（Thread Dump）：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">JVM命令</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在Linux或Mac上使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">jstack</font>`<font style="color:rgba(0, 0, 0, 0.82);">，在Windows上使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">Ctrl+Break</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>

```java
jstack -l <pid> > threaddump.txt
```

+ **<font style="color:rgba(0, 0, 0, 0.82);">IDE支持</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用Eclipse或IntelliJ的调试功能来生成线程转储。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">3. 分析线程转储</font>
<font style="color:rgba(0, 0, 0, 0.82);">从生成的线程转储中寻找"deadlock"相关信息。Java会在发现死锁时显示类似如下的信息：</font>

```java
Found one Java-level deadlock:  
=============================  
"Thread-1":  
waiting to lock monitor 0x000000000f8cba48 (object 0x00000000d66b4db0, a java.lang.Object),  
which is held by "Thread-2"  
"Thread-2":  
waiting to lock monitor 0x000000000f8cba58 (object 0x00000000d66b4dc0, a java.lang.Object),  
which is held by "Thread-1"  

Java stack information for the threads listed above:  
===================================================  
"Thread-1":  
at com.example.MyClass.methodA(MyClass.java:10)  
                               - locked <0x00000000d66b4dc0> (a java.lang.Object)  
- waiting to lock <0x00000000d66b4db0> (a java.lang.Object)  
"Thread-2":  
at com.example.MyClass.methodB(MyClass.java:20)  
- locked <0x00000000d66b4db0> (a java.lang.Object)  
- waiting to lock <0x00000000d66b4dc0> (a java.lang.Object)
```

#### <font style="color:rgba(0, 0, 0, 0.82);">4. 代码审查</font>
<font style="color:rgba(0, 0, 0, 0.82);">根据线程转储信息，对出现死锁的代码段进行详细审查。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
public class DeadlockExample {  
    private final Object lock1 = new Object();  
    private final Object lock2 = new Object();  

    public void methodA() {  
        synchronized (lock1) {  
            System.out.println("Method A: Holding lock 1...");  
            try { Thread.sleep(100); } catch (InterruptedException e) {}  
            synchronized (lock2) {  
                System.out.println("Method A: Holding lock 2...");  
            }  
        }  
    }  

    public void methodB() {  
        synchronized (lock2) {  
            System.out.println("Method B: Holding lock 2...");  
            try { Thread.sleep(100); } catch (InterruptedException e) {}  
            synchronized (lock1) {  
                System.out.println("Method B: Holding lock 1...");  
            }  
        }  
    }  
}
```

+ <font style="color:rgba(0, 0, 0, 0.82);">问题：</font>`<font style="color:rgba(0, 0, 0, 0.82);">methodA</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">methodB</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">之间存在循环锁定。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">5. 重现问题</font>
<font style="color:rgba(0, 0, 0, 0.82);">在测试环境中尝试重现死锁问题：</font>

```java
public class DeadlockDemo {  
    public static void main(String[] args) {  
        DeadlockExample example = new DeadlockExample();  

        Thread t1 = new Thread(() -> example.methodA(), "Thread-1");  
        Thread t2 = new Thread(() -> example.methodB(), "Thread-2");  

        t1.start();  
        t2.start();  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">6. 使用调试工具</font>
<font style="color:rgba(0, 0, 0, 0.82);">利用调试工具如Eclipse、IntelliJ的线程调试功能，进一步定位死锁位置。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">7. 优化和验证</font>
**<font style="color:rgba(0, 0, 0, 0.82);">解决方案</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">锁排序</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确保所有线程以相同顺序申请锁。</font>

```java
public void methodA() {  
    synchronized (lock1) {  
        synchronized (lock2) {  
            // 操作  
        }  
    }  
}  

public void methodB() {  
    synchronized (lock1) { // 调整顺序  
        synchronized (lock2) {  
            // 操作  
        }  
    }  
}
```

+ **<font style="color:rgba(0, 0, 0, 0.82);">使用</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">Lock</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">接口</font>**<font style="color:rgba(0, 0, 0, 0.82);">：用显式锁代替内置同步。</font>

```java
private final Lock lock1 = new ReentrantLock();  
private final Lock lock2 = new ReentrantLock();  

public void methodA() {  
    lock1.lock();  
    try {  
        lock2.lock();  
        try {  
            // 操作  
        } finally {  
            lock2.unlock();  
        }  
    } finally {  
        lock1.unlock();  
    }  
}  

public void methodB() {  
    lock1.lock(); // 确保同样顺序的锁定  
    try {  
        lock2.lock();  
        try {  
            // 操作  
        } finally {  
            lock2.unlock();  
        }  
    } finally {  
        lock1.unlock();  
    }  
}
```

+ **<font style="color:rgba(0, 0, 0, 0.82);">超时尝试</font>**<font style="color:rgba(0, 0, 0, 0.82);">：利用</font>`<font style="color:rgba(0, 0, 0, 0.82);">tryLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">的时间限制功能。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">验证</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">实施改动后，重新执行测试，确保死锁不再出现。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">强化单元测试以覆盖多线程场景。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">排查Java死锁问题涉及识别现象、获取详细线程转储信息、分析代码、优化同步逻辑等步骤。在实践中，通过良好的设计可以避免大多数常见的死锁问题。使用高层次的并发工具（如</font>`<font style="color:rgba(0, 0, 0, 0.82);">java.util.concurrent</font>`<font style="color:rgba(0, 0, 0, 0.82);">包）也能显著简化并发编程，从而减少死锁的可能性。持续的监控和测试是确保应用稳定性的关键。</font>



> 更新: 2024-08-23 22:11:07  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hmdc25fizw0n6hlr>