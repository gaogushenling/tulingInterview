# Java进程占用的内存有哪些部分

<font style="color:rgba(0, 0, 0, 0.82);">Java进程占用的内存主要包括以下几个部分：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">堆内存（Heap Memory）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">用于存储对象实例和数组，是JVM进行自动内存管理的主要区域。垃圾回收（GC）在此区域中进行。堆内存可以通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xms</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">-Xmx</font>`<font style="color:rgba(0, 0, 0, 0.82);">选项进行配置，分别设置初始堆大小和最大堆大小。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">方法区（Method Area）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">存放类的元数据，包括类的信息、常量、静态变量和即时编译器编译后代码。在Java 8之前，它被称为永久代（PermGen），而在Java 8及之后版本中，被称为元空间（Metaspace）。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">栈内存（Stack Memory）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">用于线程的生命周期中，存储方法调用和局部变量，每个线程都有自己的栈。栈内存自动分配和释放，不由GC管理。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">本地方法栈（Native Method Stack）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">容纳了调用Native方法（即非Java代码如C/C++代码）的线程栈，它是与操作系统相关的。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">程序计数器（Program Counter Register）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">每个线程都有自己的程序计数器，它指示当前正在执行的字节码指令地址。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">直接内存（Direct Memory）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">直接内存是在Java中使用NIO（Non-blocking IO）进行高性能I/O时使用的。它使用的是操作系统的内存，绕过了JVM的堆和堆外内存。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
```java
public class MemoryUsageExample {  
    public static void main(String[] args) {  
        // 堆内存使用：创建对象实例  
        for (int i = 0; i < 10000; i++) {  
            Person person = new Person("Name" + i, i);  
        }  

        // 栈内存使用：进行局部变量的递归调用  
        calculateFactorial(10);  

        // 直接内存使用：NIO  
        ByteBuffer buffer = ByteBuffer.allocateDirect(1024);  

        // 模拟休眠以便观察内存占用情况  
        try {  
            Thread.sleep(10000);  // 睡眠10秒  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  

    private static int calculateFactorial(int number) {  
        if (number <= 1) {  
            return 1;  
        } else {  
            return number * calculateFactorial(number - 1);  
        }  
    }  
}  

class Person {  
    String name;  
    int age;  

    Person(String name, int age) {  
        this.name = name;  
        this.age = age;  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">内存部分的解释</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">堆内存</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在内存分配的循环中创建了多个实例对象，它们存储于堆内存中。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">栈内存</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">calculateFactorial</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法进行递归调用，每次调用在栈上创建新的栈帧以存储局部变量和返回信息。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">直接内存</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuffer.allocateDirect(1024)</font>`<font style="color:rgba(0, 0, 0, 0.82);">分配直接内存，通常用于高性能I/O操作。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">方法区</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">Person</font>`<font style="color:rgba(0, 0, 0, 0.82);">类的信息、字符串“Name”等常量存储在方法区。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">本地方法栈和程序计数器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：此示例中没有演示本地方法栈的显式使用，然而程序执行过程中JVM内部正在使用程序计数器。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">Java进程的内存管理非常复杂和高效，通过不同区域的管理实现内存的高效使用和垃圾收集的管理。了解这些内存的分配和使用，是优化Java应用程序性能和调试内存问题的重要基础。</font>



> 更新: 2024-08-09 15:51:41  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/mwh0psoz0g158miq>