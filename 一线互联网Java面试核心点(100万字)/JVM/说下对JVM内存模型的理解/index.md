# 说下对JVM内存模型的理解

<font style="color:rgba(0, 0, 0, 0.82);">JVM内存模型主要指的是Java虚拟机在运行时管理内存的方式，以及它如何保证线程之间规范而安全地共享数据。JVM内存模型可分为几个关键区域，各自负责不同的功能：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">JVM内存结构</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">程序计数器（Program Counter Register）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">每个线程都有自己的程序计数器，是一个小内存空间。它记录了当前线程执行的字节码的地址，帮助CPU知道下一步要执行的指令是哪个。这也是唯一一个在JVM规范中不报内存溢出的区域。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">Java虚拟机栈（Java Virtual Machine Stack）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">每个线程也有自己独立的虚拟机栈。栈中保存着由一个个栈帧（Stack Frame）组成的信息。每个栈帧对应一次方法调用，保存局部变量表、操作数栈、动态链接、方法出口等信息。当方法执行完后，栈帧会弹出。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">本地方法栈（Native Method Stack）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">与虚拟机栈类似，但用于处理本机方法。Java程序使用JNI（Java Native Interface）调用其他语言（如C/C++）编写的程序，此时本地方法栈会被使用。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">堆（Heap）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">JVM用来存储对象实例和数组。它是所有线程共享的区域，也是垃圾回收的主要区域。Java堆由多个代（年轻代、老年代）构成，这是为了提高GC效率。所有Java对象的实例都在这里分配内存。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">方法区（Method Area）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">也是线程共享的，用于存储已被JVM加载的类信息、常量、静态变量，即时编译后的代码等。Java 8开始将方法区移出了堆内存，成为了一个独立的元空间（Metaspace），并存储在本地内存中。</font>

### 


> 更新: 2024-08-11 13:35:37  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/na254w56n5uogg0b>