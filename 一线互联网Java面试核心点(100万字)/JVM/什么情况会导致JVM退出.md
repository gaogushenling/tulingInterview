# 什么情况会导致JVM退出

<font style="color:rgba(0, 0, 0, 0.82);">Java虚拟机（JVM）退出通常是由以下几个原因导致的：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">正常程序终止</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">当程序执行完</font>`<font style="color:rgba(0, 0, 0, 0.82);">main</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法，包括所有非守护线程都终止时，JVM将正常退出。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">调用</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">System.exit(int status)</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">显式调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">System.exit()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法，以指定的状态码终止当前运行的Java虚拟机。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">未捕获的异常或错误</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果某个线程抛出的异常没有被捕获，并且此异常传播到了主线程，JVM可能会终止。</font>
4. `**<font style="color:rgba(0, 0, 0, 0.82);">Runtime.halt(int)</font>**`**<font style="color:rgba(0, 0, 0, 0.82);">或崩溃</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">直接调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">Runtime.halt()</font>`<font style="color:rgba(0, 0, 0, 0.82);">会立即停止Java进程，类似于突然终止程序而不调用任何钩子。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">JVM的致命错误（如内存访问违规）也可能导致崩溃并退出。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">外部命令强制关闭</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">例如通过操作系统的任务管理器或者控制台命令，如</font>`<font style="color:rgba(0, 0, 0, 0.82);">kill</font>`<font style="color:rgba(0, 0, 0, 0.82);">命令。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
```java
public class JVMExitExample {  
    public static void main(String[] args) {  
        // 情况1：正常程序终止  
        System.out.println("Program starts...");  

        // 打开注释逐一尝试其他退出情况  

        // 情况2：显式调用System.exit()  
        // System.exit(0);  

        // 情况3：未捕获的异常  
        // willCauseException();  

        // 情况4：调用Runtime.halt()（高风险）  
        // Runtime.getRuntime().halt(0);  

        System.out.println("Program ends...");  
    }  

    private static void willCauseException() {  
        // 产生一个未捕获的异常，导致JVM退出  
        throw new RuntimeException("Unexpected Error!");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">解释</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">正常程序终止</font>**<font style="color:rgba(0, 0, 0, 0.82);">：默认情况下，</font>`<font style="color:rgba(0, 0, 0, 0.82);">main</font>`<font style="color:rgba(0, 0, 0, 0.82);">执行完毕后，所有非守护线程完成，程序正常退出。</font>
+ `**<font style="color:rgba(0, 0, 0, 0.82);">System.exit()</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">System.exit(0)</font>`<font style="color:rgba(0, 0, 0, 0.82);">立即终止JVM。非零参数值通常被用来指示异常终止。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">未捕获的异常</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在</font>`<font style="color:rgba(0, 0, 0, 0.82);">willCauseException()</font>`<font style="color:rgba(0, 0, 0, 0.82);">中，我们故意抛出一个未捕获的</font>`<font style="color:rgba(0, 0, 0, 0.82);">RuntimeException</font>`<font style="color:rgba(0, 0, 0, 0.82);">，导致程序退出。</font>
+ `**<font style="color:rgba(0, 0, 0, 0.82);">Runtime.halt()</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：直接使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">Runtime.halt()</font>`<font style="color:rgba(0, 0, 0, 0.82);">强行终止虚拟机。这不会调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">finalize</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法或执行任何待处理的关闭钩子，也推荐在高风险操作或崩溃模拟时使用。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">外部强制关闭</font>**<font style="color:rgba(0, 0, 0, 0.82);">：不演示于代码中，需通过操作系统进行操作。</font>

<font style="color:rgba(0, 0, 0, 0.82);">每种退出机制的影响和使用场合都不同，开发者应根据程序需求谨慎地选择合适的退出方式。特别是涉及到异常退出时，通常伴随可能尚未完成的重要资源清理工作，因此合理处理异常显得尤为重要。</font>



> 更新: 2024-08-09 15:53:19  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xi7gfwcknuymk8vs>