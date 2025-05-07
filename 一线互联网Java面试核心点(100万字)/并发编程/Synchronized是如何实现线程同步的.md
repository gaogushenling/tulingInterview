# Synchronized是如何实现线程同步的

`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">关键字是Java中实现线程同步的基本机制之一。它用于保护共享资源不被多个线程同时访问，从而避免数据不一致和保证线程安全。</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">可以用于方法和代码块，有助于确保在同一时刻只有一个线程可以进入同步代码块。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">实现原理</font>
`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">基于监视器锁（Monitor Lock）实现，底层依赖于操作系统的互斥锁（Mutex）。每个对象在Java中都与一个隐式的监视器相关联，使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">时，线程需要获得对象的监视器锁，然后才能进入同步代码块。</font>

<font style="color:rgba(0, 0, 0, 0.82);">Java的对象有一个头（Object Header），其中包含一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Mark Word</font>`<font style="color:rgba(0, 0, 0, 0.82);">，用于存储锁的信息。锁有不同的状态，包括无锁状态、偏向锁、轻量级锁和重量级锁，JVM会根据竞争情况自动进行状态变换以优化性能。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用示例</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. 同步方法</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">直接修饰实例方法或静态方法，以同步整个方法。下面是一个示例：</font>

```java
public class Counter {  
    private int count = 0;  

    // Synchronized instance method  
    public synchronized void increment() {  
        count++;  
    }  

    public synchronized int getCount() {  
        return count;  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">在这个示例中，</font>`<font style="color:rgba(0, 0, 0, 0.82);">increment()</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">getCount()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法是同步的，这意味着对同一实例的这些方法调用是线程安全的。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">2. 同步代码块</font>
<font style="color:rgba(0, 0, 0, 0.82);">为了减少锁的粒度，您可以在方法中使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">代码块，从而只锁住特定的部分。</font>

```java
public class Counter {  
    private int count = 0;  
    private final Object lock = new Object(); // 使用自定义锁对象  

    public void increment() {  
        synchronized(lock) {  
            count++;  
        }  
    }  

    public int getCount() {  
        synchronized(lock) {  
            return count;  
        }  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">这种方法允许更精细地控制同步范围，常用于只需保护代码的一部分而非整个方法的场景。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">同步静态方法</font>
<font style="color:rgba(0, 0, 0, 0.82);">对于静态方法，</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">锁定的是类对象，因为静态方法属于类，而不是实例。</font>

```java
public class Counter {  
    private static int count = 0;  

    // Synchronized static method  
    public static synchronized void increment() {  
        count++;  
    }  

    public static synchronized int getCount() {  
        return count;  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">在这个例子中，加锁保护的是整个类</font>`<font style="color:rgba(0, 0, 0, 0.82);">Counter</font>`<font style="color:rgba(0, 0, 0, 0.82);">的静态成员</font>`<font style="color:rgba(0, 0, 0, 0.82);">count</font>`<font style="color:rgba(0, 0, 0, 0.82);">，使用类级别的锁来保证线程安全。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">注意事项</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">可能导致线程阻塞，从而影响程序性能。尽量将</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">锁的范围缩小，以减少锁竞争。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">死锁风险</font>**<font style="color:rgba(0, 0, 0, 0.82);">：错误使用同步可能导致死锁。例如，多个线程在不同的锁上循环等待。要避免持有多个锁，或者确保获取锁的顺序一致。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">原子性和可见性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">不仅保证代码块的原子性，还保证进入同步代码块之前对变量的修改，对于其他线程是可见的。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">公平性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">不保证线程获取锁的公平性，即线程获取锁的顺序不一定按照调用顺序。如果需要严格的公平性控制，可能需要使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">ReentrantLock</font>`<font style="color:rgba(0, 0, 0, 0.82);">等高级锁。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">synchronized</font>`<font style="color:rgba(0, 0, 0, 0.82);">，我们可以确保共享资源在多线程环境下的安全访问，同时通过合适的使用方式来优化性能和避免常见的多线程问题。</font>



> 更新: 2024-08-11 19:10:04  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/bwxdoap45q8ozbtm>