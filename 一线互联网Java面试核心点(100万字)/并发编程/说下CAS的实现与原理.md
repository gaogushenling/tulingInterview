# 说下CAS的实现与原理

<font style="color:rgba(0, 0, 0, 0.82);">CAS（Compare-And-Swap）是一种用于多线程编程中的原子操作，它通过硬件支持的指令来实现无锁同步。在多线程环境下，CAS操作通过比较和交换来更新变量的值，是一种乐观锁的实现方式，可以避免锁机制带来的开销。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">CAS工作原理</font>
<font style="color:rgba(0, 0, 0, 0.82);">CAS操作涉及三个操作数：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">内存位置V</font>**<font style="color:rgba(0, 0, 0, 0.82);">：需要修改的变量的内存地址。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">预期值A</font>**<font style="color:rgba(0, 0, 0, 0.82);">：期望变量当前持有的值。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">新值B</font>**<font style="color:rgba(0, 0, 0, 0.82);">：需要更新到变量的新值。</font>

<font style="color:rgba(0, 0, 0, 0.82);">CAS工作过程如下：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">比较内存位置V的当前值是否等于预期值A。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">如果是，则用新值B更新内存位置中的值。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">如果不是，则不更新值，并返回当前实际值。</font>

<font style="color:rgba(0, 0, 0, 0.82);">这个操作是原子的，硬件保证比较并更新的操作不会被中断。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">CAS在Java中的实现</font>
<font style="color:rgba(0, 0, 0, 0.82);">在Java中，CAS操作主要通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">java.util.concurrent.atomic</font>`<font style="color:rgba(0, 0, 0, 0.82);">包中的类来实现。例如，</font>`<font style="color:rgba(0, 0, 0, 0.82);">AtomicInteger</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">AtomicBoolean</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">AtomicReference</font>`<font style="color:rgba(0, 0, 0, 0.82);">等。通过这些类的操作，Java应用可以在多线程环境下安全地对基本数据类型进行操作，而无需显式锁定。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">示例：使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">AtomicInteger</font>`
`<font style="color:rgba(0, 0, 0, 0.82);">AtomicInteger</font>`<font style="color:rgba(0, 0, 0, 0.82);">类提供了以原子方式更新</font>`<font style="color:rgba(0, 0, 0, 0.82);">int</font>`<font style="color:rgba(0, 0, 0, 0.82);">型变量的方法，其中</font>`<font style="color:rgba(0, 0, 0, 0.82);">compareAndSet</font>`<font style="color:rgba(0, 0, 0, 0.82);">是CAS操作实现的核心方法。</font>

```java
import java.util.concurrent.atomic.AtomicInteger;  

public class CASExample {  
    private AtomicInteger atomicInteger = new AtomicInteger(0);  

    public void increment() {  
        int expectedValue;  
        int newValue;  
        do {  
            expectedValue = atomicInteger.get(); // 获取当前值  
            newValue = expectedValue + 1;        // 计算新值  
        } while (!atomicInteger.compareAndSet(expectedValue, newValue)); // CAS操作  
    }  

    public int getValue() {  
        return atomicInteger.get();  
    }  

    public static void main(String[] args) {  
        CASExample example = new CASExample();  
        example.increment();  
        System.out.println("Value after increment: " + example.getValue());  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">在这个示例中，</font>`<font style="color:rgba(0, 0, 0, 0.82);">increment</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法尝试获取当前值、计算新值并使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">compareAndSet</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法进行更新。</font>`<font style="color:rgba(0, 0, 0, 0.82);">compareAndSet</font>`<font style="color:rgba(0, 0, 0, 0.82);">返回</font>`<font style="color:rgba(0, 0, 0, 0.82);">true</font>`<font style="color:rgba(0, 0, 0, 0.82);">表示更新成功，返回</font>`<font style="color:rgba(0, 0, 0, 0.82);">false</font>`<font style="color:rgba(0, 0, 0, 0.82);">表示需要重试。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">优势与考虑</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">无锁机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：CAS允许许多线程尝试更新同一个变量，而无需锁定，大大减小了锁竞争。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能提升</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在无锁的情况下，通常会有更好的性能表现，适合高并发场景。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">ABA问题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：CAS无法直接解决ABA问题（一个值从A变到B，又变回A），可以使用带版本号的变量如</font>`<font style="color:rgba(0, 0, 0, 0.82);">AtomicStampedReference</font>`<font style="color:rgba(0, 0, 0, 0.82);">来解决这个问题。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">活锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在高争用环境下，如果许多线程不断重试更新操作，可能会导致活锁，程序一直循环重试而得不到进展。</font>

<font style="color:rgba(0, 0, 0, 0.82);">CAS是实现乐观并发控制的基础技术，它在Java的并发编程中起着非常重要的作用，尤其是在实现无锁算法时。通过适当使用CAS，可以在保证线程安全的同时，减少锁竞争，提高程序性能。</font>



> 更新: 2024-08-11 22:00:22  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rf6z3cwdvsus3wk3>