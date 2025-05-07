# volatile有哪些应用场景？

`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 是 Java 中一个非常重要的关键字，它主要用于多线程编程，确保变量在多个线程之间的</font>**<font style="color:#DF2A3F;">可见性</font>**<font style="color:rgba(6, 8, 31, 0.88);">和</font>**<font style="color:#DF2A3F;">禁止指令重排序（有序性）</font>**<font style="color:rgba(6, 8, 31, 0.88);">。以下是一些常见的应用场景：</font>

### <font style="color:rgba(6, 8, 31, 0.88);">1. 状态标志——可见性</font>
<font style="color:rgba(6, 8, 31, 0.88);">当一个线程需要等待其他线程的状态改变时，可以使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">关键字来定义状态标志。这样，所有线程都能及时看到状态的变化。</font>

```java
java



public class VolatileExample {  
    private volatile boolean running = true;  

    public void stop() {  
        running = false; // 修改状态标志  
    }  

    public void run() {  
        while (running) {  
            // 线程执行逻辑  
        }  
    }  
}
```

### <font style="color:rgba(6, 8, 31, 0.88);">2. 双重检查锁定的实现——有序性</font>
<font style="color:rgba(6, 8, 31, 0.88);">在单例模式中，使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">变量和双重检查锁定以确保实例在多线程环境中的正确性和惰性初始化。</font>

```java
java



public class Singleton {  
    private static volatile Singleton instance;  

    private Singleton() {}  

    public static Singleton getInstance() {  
        if (instance == null) {  
            synchronized (Singleton.class) {  
                if (instance == null) {  
                    instance = new Singleton(); // volatile 确保对象的正确构造  
                }  
            }  
        }  
        return instance;  
    }  
}
```

### <font style="color:rgba(6, 8, 31, 0.88);">3. 读写状态的模式——可见性</font>
<font style="color:rgba(6, 8, 31, 0.88);">在某些应用中，可能需要让一个线程写入一个值，而其他线程读取这个值。使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">可以确保数据在线程间的可见性。</font>

```java
java



public class Counter {  
    private volatile int count = 0;  

    public void increment() {  
        count++; // 不安全， volatile 不能保证原子性  
    }  

    public int getCount() {  
        return count;  
    }  
}
```

<font style="color:rgba(6, 8, 31, 0.88);">注意：虽然</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">能保证可见性，但它无法保证操作的原子性，因此在这种情况下，可以考虑使用其他机制，如</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">AtomicInteger</font>`<font style="color:rgba(6, 8, 31, 0.88);">。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">4. 轻量级的锁替代——可见性</font>
<font style="color:rgba(6, 8, 31, 0.88);">对于某些简化的线程安全场景，</font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">可以作为轻量级锁的替代，尤其是在只需确保可见性的地方，避免了复杂的锁机制。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">5. 状态机中的状态共享——可见性</font>
<font style="color:rgba(6, 8, 31, 0.88);">在实现状态机时，可以使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">修饰表示当前状态的变量，确保在状态变更时被多个线程感知。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">6. 与其他线程协作——可见性</font>
<font style="color:rgba(6, 8, 31, 0.88);">在多线程环境中，如果需要通知其他线程某个条件的变化，可以使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">变量来进行线程间的协作。</font>

```java
java



public class Notifier {  
    private volatile boolean notified = false;  

    public void watch() throws InterruptedException {  
        while (!notified) {  
            // 等待通知  
        }  
        System.out.println("Notified!");  
    }  

    public void notifyWatchers() {  
        notified = true; // 通知  
    }  
}
```

### <font style="color:rgba(6, 8, 31, 0.88);">小结</font>
`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 在 Java 中是一种非常重要的工具，适用于多线程场景中需要保证共享变量</font>**<font style="color:#DF2A3F;">可见性</font>**<font style="color:rgba(6, 8, 31, 0.88);">和</font>**<font style="color:#DF2A3F;">防止指令重排序</font>**<font style="color:rgba(6, 8, 31, 0.88);">的情况。虽然 </font>`<font style="color:rgba(6, 8, 31, 0.88);">volatile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 对于简单状态标志和读取共享状态非常有用，但对于复杂的并发控制，建议考虑使用 </font>`<font style="color:rgba(6, 8, 31, 0.88);">synchronized</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 或其他并发工具（如 </font>`<font style="color:rgba(6, 8, 31, 0.88);">Locks</font>`<font style="color:rgba(6, 8, 31, 0.88);">、</font>`<font style="color:rgba(6, 8, 31, 0.88);">Atomic</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 类等）。</font>

<font style="color:rgba(6, 8, 31, 0.88);">如果需要详细的代码演示或进一步解释，随时告诉我！</font>



> 更新: 2024-12-27 15:48:17  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/mxa0qbe0utm8m6kf>