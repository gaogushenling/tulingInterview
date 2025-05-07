# 对象一定分配在堆中吗

<font style="color:rgba(0, 0, 0, 0.82);">在Java中，传统上我们认为对象是在堆上分配内存的。但是，随着JVM优化技术的发展，尤其是在引入即时编译器（JIT）和逃逸分析（Escape Analysis）技术后，并非所有对象都一定在堆上分配内存。这有以下一些细节：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">逃逸分析（Escape Analysis）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">逃逸分析是一种优化技术，用于确定对象的作用范围。如果JVM通过逃逸分析确定一个对象不会被方法之外的代码访问（即对象不会逃逸出方法），那么JVM可能会选择在栈上分配该对象。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">栈上分配（Stack Allocation）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果对象可以被确定为不会逃逸出其方法，则JVM可以在栈上为该对象分配内存。这减少了垃圾回收的压力，因为栈上的内存在方法执行结束后自动释放。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">标量替换（Scalar Replacement）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果对象的所有属性都可以独立处理，JVM可能会对对象进行标量替换，将对象分解为其基本类型的成员变量进行优化。这种情况下，原始的对象概念被消除，更谈不上在堆或栈上分配。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">寄存器分配（Registers Allocation）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在某些情况下，JIT编译器甚至可能将某些对象的内容存放在CPU寄存器中，以提高访问速度。</font>



**<font style="color:rgba(0, 0, 0, 0.82);">示例</font>**<font style="color:rgba(0, 0, 0, 0.82);">：下面是一个简单的代码示例说明逃逸分析可能的影响。</font>

```java
public class EscapeAnalysisExample {  

    public void foo() {  
        Point p = new Point(1, 2);  
        System.out.println(p.x + p.y);  
    }  

    class Point {  
        int x;  
        int y;  

        Point(int x, int y) {  
            this.x = x;  
            this.y = y;  
        }  
    }  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">在上面的代码中，如果JVM通过逃逸分析确定</font>`<font style="color:rgba(0, 0, 0, 0.82);">Point p</font>`<font style="color:rgba(0, 0, 0, 0.82);">对象只在</font>`<font style="color:rgba(0, 0, 0, 0.82);">foo</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法中使用，它可能会选择在栈上分配</font>`<font style="color:rgba(0, 0, 0, 0.82);">p</font>`<font style="color:rgba(0, 0, 0, 0.82);">。由于Point对象里就两个int类型的变量，所以JVM还会对Point对象做标量替换，把整个对象就当做int类型的两个变量x和y放在局部变量表里，这样性能更高，都不会在栈上分配对象。当然如果Point对象有成员变量是对象的则不会被标量替换。</font>



<font style="color:rgba(0, 0, 0, 0.82);">总结来说，现代JVM有能力优化对象的内存分配策略，并不局限于堆内存，一些短暂的、不会逃逸出方法的对象可以被分配在栈上甚至被消除。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"></font>

<font style="color:rgb(0, 0, 0);">  
</font>



> 更新: 2024-08-09 14:43:11  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/io1zauwmbp314aa4>