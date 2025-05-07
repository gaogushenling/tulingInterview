# GC是任意时候都能进行的吗

<font style="color:rgba(0, 0, 0, 0.82);">GC垃圾收集只能在</font>**<font style="color:rgba(0, 0, 0, 0.82);">安全点</font>**<font style="color:rgba(0, 0, 0, 0.82);">才能进行。在Java虚拟机（JVM）中，**安全点（Safe Point）**是程序执行的某些特定位置。JVM只能在安全点安全地暂停执行，从而进行垃圾回收（GC）等操作。安全点的设定确保了当线程暂停时，程序的状态是可知和一致的。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">作用</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">垃圾收集</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在进行垃圾收集时，JVM需要暂停所有应用程序线程（GC暂停），以确保不会有线程在操作内存。同时，状态的快照是确定的，以便于GC工作。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">堆栈遍历</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在执行如线程转储（Thread Dump）等操作时，JVM需要安全地遍历线程栈，这时也需要安全点。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">性能损耗最小化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通过在最可能长时间运行的指令设置安全点（例如循环的末端、方法的调用与返回），JVM可以减少程序暂停的频率，从而降低性能损耗。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">安全点的触发条件</font>
<font style="color:rgba(0, 0, 0, 0.82);">安全点一般插入在以下几种情况：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">方法调用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：每次方法调用都是一个潜在的安全点。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">循环回跳</font>**<font style="color:rgba(0, 0, 0, 0.82);">：长时间循环中间会插入安全点检查。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">异常处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：处理异常时，也会检查是否到达安全点。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">实际执行中的安全点示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">考虑下面的代码，JVM 在执行时，会对其中的多个位置进行安全点插入：</font>

```java
public class SafePointExample {  

    public static void main(String[] args) {  
        long sum = 0;  
        System.out.println("Start calculation...");  

        for (int i = 0; i < Integer.MAX_VALUE; i++) {  
            sum += i;  
            // 有可能成为安全点，因为这是一个明显的循环回跳  
        }  

        System.out.println("Sum: " + sum);  
        // 方法结束的时候，这里可能成为一个安全点  
    }  
}
```

 

### <font style="color:rgba(0, 0, 0, 0.82);">重要性</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">最小化暂停时间</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过仅在安全点暂停线程，可最大程度减少程序因为GC等操作而暂停执行的时间。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能和一致性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：安全点设计能够确保为GC等需要程序一致性暂停的操作提供合适控制，同时尽可能保持程序性能。</font>

<font style="color:rgba(0, 0, 0, 0.82);">总体来说，安全点是JVM优化执行环境的一部分，不同JVM实现可能有不同的安全点管理策略以权衡性能与系统一致性。</font>



> 更新: 2024-08-11 16:27:01  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/visq4rsmvfgex14b>