# 说下JVM中一次完整的 GC 流程

<font style="color:rgba(0, 0, 0, 0.82);">Java Virtual Machine (JVM) 中的垃圾回收（GC）机制是自动内存管理的一部分，负责回收不再被引用的对象所占用的内存空间。理解完整的 GC 流程有助于优化 Java 程序的性能。以下是 JVM 中一次完整的 GC 流程：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">JVM 中的内存区域</font>
<font style="color:rgba(0, 0, 0, 0.82);">JVM 的内存区域主要分为以下几块：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">堆（Heap）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：存放对象实例，GC 的主要工作区域。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">栈（Stack）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：存放方法参数和局部变量，方法执行时创建销毁。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">方法区（Method Area）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：存放类信息、常量、静态变量等。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">程序计数器（PC Register）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：线程执行的字节码行号指示器。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">垃圾回收的区域</font>
<font style="color:rgba(0, 0, 0, 0.82);">垃圾回收主要在堆中进行，堆又分为年轻代（Young Generation）、老年代（Old Generation）和永久代（JDK 8 及以后的 Metaspace 取代了永久代）。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">垃圾回收的步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">标记（Marking）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：找到所有活动对象。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">清除（Sweeping）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：清理未被标记的对象，从而释放内存。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">压缩（Compaction）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对内存进行整理，使得活跃对象移动到一起，避免碎片化（这一步并不是所有垃圾回收算法的必须步骤，如标记-清除算法就不需要此步骤）。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">垃圾回收算法</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">标记-清除</font>**<font style="color:rgba(0, 0, 0, 0.82);">：首先标记存活对象，然后清除未被标记的对象。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">标记-复制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：将存活对象复制到新的内存区域，适合于年轻代。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">标记-压缩</font>**<font style="color:rgba(0, 0, 0, 0.82);">：标记后，将活跃对象压缩到一起，清除后续无用区。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">垃圾回收器示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">下面是使用 Java 的</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">System.gc()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">进行垃圾回收的一个简单代码示例：</font>

```java
public class GCDemo {  

    public static void main(String[] args) throws InterruptedException {  
        System.out.println("Starting GC Demo...");  

        // Allocate a lot of memory  
        for (int i = 0; i < 1000; i++) {  
            byte[] array = new byte[1024 * 1024]; // 1 MB  
            System.out.println("Allocated 1MB, index: " + i);  
            Thread.sleep(10); // Slow down allocation  
        }  

        System.out.println("Requesting GC...");  
        // Requesting Garbage Collection  
        System.gc(); // Suggests the JVM to perform GC  

        // Waiting for a short while to visualize GC activity  
        Thread.sleep(5000);  

        System.out.println("Finished GC Demo...");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">分析</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">对象分配</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">main</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法中，大量地分配内存给一个</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">byte[]</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">数组。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">触发 GC</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">System.gc()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">请求垃圾回收，这不是强制的，但 JVM 会尽量去执行垃圾回收。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">垃圾回收过程</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">JVM 可能会先尝试进行 Minor GC，回收年轻代对象。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果内存不足，可能触发 Major GC 或 Full GC，回收年轻代 + 老年代对象。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">观察垃圾回收</font>**<font style="color:rgba(0, 0, 0, 0.82);">：建议在 JVM 参数中增加</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">-verbose:gc</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">或者使用更详细的 GC 日志选项如</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">-XX:+PrintGCDetails</font>`<font style="color:rgba(0, 0, 0, 0.82);">，以更好地观察垃圾回收过程：</font>

```java
java -verbose:gc -XX:+PrintGCDetails GCDemo
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">年轻代和老年代的区别</font>**<font style="color:rgba(0, 0, 0, 0.82);">：年轻代是短生命周期对象的汇聚地，常被 Minor GC；老年代保存生命周期较长的对象，发生 Major 或 Full GC。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">分代回收算法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：JVM 的分代回收策略最大化性能，年轻代使用复制收集算法，高效清理短命对象；老年代使用标记-整理算法，处理长命对象。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">优化 GC 性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通常通过调整堆大小和代大小，以及选择适合的垃圾收集器（如 G1、CMS、Parallel GC 等）来优化 GC 的性能。</font>



> 更新: 2024-08-09 16:05:26  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/qy0y5pewbrrvd926>