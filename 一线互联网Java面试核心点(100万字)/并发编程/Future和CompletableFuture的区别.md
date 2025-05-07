# Future和CompletableFuture的区别

`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 和 </font>`<font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 是 Java 中用于异步编程的两个重要接口，它们都在 </font>`<font style="color:rgba(0, 0, 0, 0.82);">java.util.concurrent</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 包中被定义，但它们的设计和用途有显著的不同。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">Future</font>
`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">接口代表了一个异步计算的结果。它提供了一种检查计算是否完成的方法，以及在计算完成后获取结果或处理可能的异常。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">特点</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">获取结果</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">get()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法阻塞等待计算完成并获取结果。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">取消任务</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">cancel()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法尝试取消任务。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">异步但不易组合</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">本身没有提供处理异步结果的方法，也没有组合多个</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">的机制。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
```java
import java.util.concurrent.Callable;  
import java.util.concurrent.ExecutionException;  
import java.util.concurrent.ExecutorService;  
import java.util.concurrent.Executors;  
import java.util.concurrent.Future;  

public class FutureExample {  
    public static void main(String[] args) {  
        ExecutorService executor = Executors.newFixedThreadPool(2);  

        Callable<Integer> task = () -> {  
            Thread.sleep(1000); // 模拟长时间任务  
            return 42;  
        };  

        Future<Integer> future = executor.submit(task);  

        try {  
            // do something else  
            System.out.println("Doing something else...");  
            Integer result = future.get(); // 阻塞等待结果  
            System.out.println("Result: " + result);  
        } catch (InterruptedException | ExecutionException e) {  
            e.printStackTrace();  
        } finally {  
            executor.shutdown();  
        }  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>
`<font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">是 Java 8 引入的新特性，支持更多的异步编程功能和更丰富的组合手段。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">特点</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">非阻塞</font>**<font style="color:rgba(0, 0, 0, 0.82);">：提供了很多方法如</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">thenApply()</font>`<font style="color:rgba(0, 0, 0, 0.82);">,</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">thenAccept()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">等进行异步计算，避免阻塞。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">组合任务</font>**<font style="color:rgba(0, 0, 0, 0.82);">：支持组合多个异步操作，如使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">thenCombine()</font>`<font style="color:rgba(0, 0, 0, 0.82);">,</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">allOf()</font>`<font style="color:rgba(0, 0, 0, 0.82);">,</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">anyOf()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">等。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">手动完成</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">complete()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法手动设置结果。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">与流式操作结合</font>**<font style="color:rgba(0, 0, 0, 0.82);">：支持更多的函数式操作，像流式 API 一样链式调用。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
```java
import java.util.concurrent.CompletableFuture;  
import java.util.concurrent.ExecutionException;  

public class CompletableFutureExample {  
    public static void main(String[] args) throws ExecutionException, InterruptedException {  
        CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {  
            sleep(1000);  
            return 42;  
        });  

        // 非阻塞链式操作  
        CompletableFuture<Void> resultFuture = future.thenApply(result -> {  
            System.out.println("Result: " + result);  
            return result * 2;  
        }).thenAccept(result -> System.out.println("Transformed Result: " + result));  

        resultFuture.get(); // 等待所有阶段完成  
    }  

    private static void sleep(int milliseconds) {  
        try {  
            Thread.sleep(milliseconds);  
        } catch (InterruptedException e) {  
            throw new IllegalStateException(e);  
        }  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">主要区别</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">功能范围</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">为更复杂的异步操作提供了更广泛的功能，而</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">仅提供基本的任务完成和结果获取。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">组合能力</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">支持异步任务的流式组合，而</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">需要显式的阻塞和同步。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">非阻塞</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">可以方便地进行非阻塞操作链，而</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">Future</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">本质上是阻塞的。</font>

`<font style="color:rgba(0, 0, 0, 0.82);">CompletableFuture</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 提供了更现代的接口来处理复杂的异步编程需求，它支持 completablepatables 和更丰富的组合能力，尤其适合复杂的异步数据流处理场景。</font>



> 更新: 2024-08-11 19:39:18  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vl6pon0s08fg9iol>