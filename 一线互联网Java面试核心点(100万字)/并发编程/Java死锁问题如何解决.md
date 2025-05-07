# Java死锁问题如何解决

<font style="color:rgba(0, 0, 0, 0.82);">在Java中，死锁是一种阻塞现象，发生在两个或多个线程相互持有对方所需的资源，从而导致线程无法继续执行。当线程陷入死锁状态时，就无法释放它们持有的资源，因为它们在等待其他线程释放其持有的资源。死锁的四个必要条件是：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">互斥条件</font>**<font style="color:rgba(0, 0, 0, 0.82);">：资源不能被多个线程共享。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">持有和等待</font>**<font style="color:rgba(0, 0, 0, 0.82);">：线程已经持有至少一个资源并等待获取另外的资源。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">不剥夺条件</font>**<font style="color:rgba(0, 0, 0, 0.82);">：资源不能从持有它的线程中剥夺，必须由线程自行释放。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">循环等待条件</font>**<font style="color:rgba(0, 0, 0, 0.82);">：存在一个线程循环等待链，链中的每个线程都等待下一个线程所持有的资源。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例代码造成死锁</font>
<font style="color:rgba(0, 0, 0, 0.82);">下面是一个简单的造成死锁的Java代码示例：</font>

```java
public class DeadlockExample {  

    private static class Resource {  
        private final String name;  

        public Resource(String name) {  
            this.name = name;  
        }  

        public String getName() {  
            return name;  
        }  
    }  

    private static final Resource resource1 = new Resource("Resource1");  
    private static final Resource resource2 = new Resource("Resource2");  

    public static void main(String[] args) {  
        Thread thread1 = new Thread(() -> {  
            synchronized (resource1) {  
                System.out.println("Thread 1: locked " + resource1.getName());  

                try { Thread.sleep(100); } catch (InterruptedException e) {}  

                synchronized (resource2) {  
                    System.out.println("Thread 1: locked " + resource2.getName());  
                }  
            }  
        });  

        Thread thread2 = new Thread(() -> {  
            synchronized (resource2) {  
                System.out.println("Thread 2: locked " + resource2.getName());  

                try { Thread.sleep(100); } catch (InterruptedException e) {}  

                synchronized (resource1) {  
                    System.out.println("Thread 2: locked " + resource1.getName());  
                }  
            }  
        });  

        thread1.start();  
        thread2.start();  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">在这个示例中，</font>`<font style="color:rgba(0, 0, 0, 0.82);">thread1</font>`<font style="color:rgba(0, 0, 0, 0.82);">首先锁定</font>`<font style="color:rgba(0, 0, 0, 0.82);">resource1</font>`<font style="color:rgba(0, 0, 0, 0.82);">然后尝试锁定</font>`<font style="color:rgba(0, 0, 0, 0.82);">resource2</font>`<font style="color:rgba(0, 0, 0, 0.82);">。同时，</font>`<font style="color:rgba(0, 0, 0, 0.82);">thread2</font>`<font style="color:rgba(0, 0, 0, 0.82);">锁定</font>`<font style="color:rgba(0, 0, 0, 0.82);">resource2</font>`<font style="color:rgba(0, 0, 0, 0.82);">后尝试锁定</font>`<font style="color:rgba(0, 0, 0, 0.82);">resource1</font>`<font style="color:rgba(0, 0, 0, 0.82);">。由于两个线程都在相互等待对方释放资源，因此会形成死锁。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">检测死锁</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">线程转储分析</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以通过JVM工具如</font>`<font style="color:rgba(0, 0, 0, 0.82);">jstack</font>`<font style="color:rgba(0, 0, 0, 0.82);">获取线程转储来识别死锁。线程转储会展示所有线程的状态以及所有锁的持有情况。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">使用JVisualVM/JConsole</font>**<font style="color:rgba(0, 0, 0, 0.82);">：这些工具可以实时监控JVM状态，并且会自动检测和报告死锁。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">预防死锁</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">资源排序（Resource Ordering）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：避免循环等待，可以提前规定资源获取的顺序。所有线程在锁定资源时，必须按预定顺序加锁。</font>

```java
private void acquireResourcesInOrder() {  
    synchronized (resource1) {  
        synchronized (resource2) {  
            // Perform operations  
        }  
    }  
}
```

2. **<font style="color:rgba(0, 0, 0, 0.82);">尝试锁定（Try-Lock）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">tryLock()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法非阻塞地获取锁。可以设置超时时间来避免死锁。</font>

```java
ReentrantLock lock1 = new ReentrantLock();  
ReentrantLock lock2 = new ReentrantLock();  

public void safeMethod() {  
    try {  
        if (lock1.tryLock(100, TimeUnit.MILLISECONDS)) {  
            try {  
                if (lock2.tryLock(100, TimeUnit.MILLISECONDS)) {  
                    try {  
                        // Perform operations  
                    } finally {  
                        lock2.unlock();  
                    }  
                }  
            } finally {  
                lock1.unlock();  
            }  
        }  
    } catch (InterruptedException e) {  
        e.printStackTrace();  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">通过适当的设计和策略，死锁是可以预防和管理的，特别是在复杂应用程序多线程编程环境中。</font>



> 更新: 2024-08-11 20:21:31  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/yhez1gsatz4004rs>