# Synchronized与ReentrantLock的区别

`<font style="color:rgba(0, 0, 0, 0.82);">Synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">都是Java中用于实现线程同步的机制，它们各有优缺点和适用场景。以下是它们的比较和适用示例：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">相似之处</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">线程安全</font>**<font style="color:rgba(0, 0, 0, 0.82);">：两者都可以用于实现对临界区的保护，从而实现线程安全。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">可重入性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：两者都支持可重入性，允许一个线程多次获取同一把锁而不会发生死锁。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">互斥锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">：本质上都是互斥锁，确保在同一时刻只有一个线程能执行被同步的代码块。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">区别</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">灵活性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">是Java语言的内置特性，使用简单，但功能有限。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">是一个类，提供了更高级的锁功能，例如：可中断的锁获取、超时获取锁、非阻塞尝试获取锁以及可实现更复杂的同步结构。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在较低竞争时，</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">会自动使用优化，比如锁消除和锁粗化，使得它的性能在某些情况下可能高于</font>`<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">可能在高竞争下表现更好，因为它可以提供非公平和公平锁模式，公平模式会严格按照请求锁的顺序来分配锁。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">实现的功能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">提供了更多控制功能，如</font>`<font style="color:rgba(0, 0, 0, 0.82);">lock()</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">unlock()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法，可在任何位置灵活调用。而</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">在语法上是强制块结束时锁自动释放。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">提供</font>`<font style="color:rgba(0, 0, 0, 0.82);">tryLock()</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">lockInterruptibly()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法，以响应中断和超时。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">条件变量</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">具有与之关联的Condition对象，可以搭配lock来更细粒度的控制线程通信。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">配合</font>`<font style="color:rgba(0, 0, 0, 0.82);">Object</font>`<font style="color:rgba(0, 0, 0, 0.82);">的</font>`<font style="color:rgba(0, 0, 0, 0.82);">wait()</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">notify()</font>`<font style="color:rgba(0, 0, 0, 0.82);">/</font>`<font style="color:rgba(0, 0, 0, 0.82);">notifyAll()</font>`<font style="color:rgba(0, 0, 0, 0.82);">来进行线程之间的通信，但不如</font>`<font style="color:rgba(0, 0, 0, 0.82);">Condition</font>`<font style="color:rgba(0, 0, 0, 0.82);">灵活。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`
```java
public class SynchronizedExample {  
    private int count = 0;  

    public synchronized void increment() {  
        count++;  
    }  

    public synchronized int getCount() {  
        return count;  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`
```java
import java.util.concurrent.locks.ReentrantLock;  

public class ReentrantLockExample {  
    private int count = 0;  
    private final ReentrantLock lock = new ReentrantLock();  

    public void increment() {  
        lock.lock(); // 锁定  
        try {  
            count++;  
        } finally {  
            lock.unlock(); // 确保释放锁  
        }  
    }  

    public int getCount() {  
        lock.lock();  
        try {  
            return count;  
        } finally {  
            lock.unlock();  
        }  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">适用场景</font>
+ `**<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：适用于简单的同步需求。由于其语法简单且嵌入在Java语言中，特别适合锁定范围与方法等价的情况。小规模、多线程竞争不高的情况下表现优异。适合开发者不想处理锁的复杂生命周期时使用。</font>
+ `**<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：适用于需要更高级的同步控制，或者锁定范围与方法不同时。特别是在需要公平锁、可中断锁操作、尝试获取带超时功能的锁，或者需要多个条件等待时，应选择</font>`<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">。当系统规模较大、线程数较多，且具有复杂同步需求的情境时表现突出。</font>

<font style="color:rgba(0, 0, 0, 0.82);">选择一个合适的锁机制，将有助于提升应用程序的性能并简化并发代码的编写。</font>



> 更新: 2024-08-11 20:59:00  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/siagvwuiuod0c9w1>