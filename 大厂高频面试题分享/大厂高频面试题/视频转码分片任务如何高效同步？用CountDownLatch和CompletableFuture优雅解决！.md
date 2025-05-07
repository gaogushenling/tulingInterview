# 视频转码分片任务如何高效同步？用 CountDownLatch 和 CompletableFuture 优雅解决！



### **<font style="color:rgba(6, 8, 31, 0.88);">需求背景</font>**
<font style="color:rgba(6, 8, 31, 0.88);">假设你负责开发一套视频转码服务，每个线程负责转码一段视频分片，所有线程转码完成后，才能汇总并合成最终的视频文件。</font>

**<font style="color:rgba(6, 8, 31, 0.88);">面临的问题：</font>**

1. **<font style="color:rgba(6, 8, 31, 0.88);">线程同步</font>**<font style="color:rgba(6, 8, 31, 0.88);">：  
</font><font style="color:rgba(6, 8, 31, 0.88);">所有分片必须完成转码后，才能进行下一步操作（如视频合成）。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">超时处理</font>**<font style="color:rgba(6, 8, 31, 0.88);">：  
</font><font style="color:rgba(6, 8, 31, 0.88);">个别线程可能因网络、IO 等问题导致超时，不能无限制等待，需容错处理。</font>

<font style="color:rgba(6, 8, 31, 0.88);">目标是：</font>**<font style="color:rgba(6, 8, 31, 0.88);">在确保主线程能够获知任务完成状态的同时，避免长时间等待。</font>**

**<font style="color:rgba(6, 8, 31, 0.88);"></font>**

### **<font style="color:rgba(6, 8, 31, 0.88);">1. 用 CountDownLatch 管理任务同步</font>**
**<font style="color:rgba(6, 8, 31, 0.88);">CountDownLatch</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">是 Java 并发包中的计数器类，可以实现多个线程的同步等待。我们可以用它跟踪所有线程的任务进度，只要所有线程完成，主任务即可继续。</font>

**<font style="color:rgba(6, 8, 31, 0.88);">代码实现：</font>**

```plain
import java.util.concurrent.*;  

public class VideoTranscodingService {  

    public static void main(String[] args) throws InterruptedException {  
        int taskCount = 5; // 分片任务数量  
        CountDownLatch latch = new CountDownLatch(taskCount); // 初始化倒计时器  

        ExecutorService executor = Executors.newFixedThreadPool(taskCount);  

        // 模拟启动多个分片任务  
        for (int i = 1; i <= taskCount; i++) {  
            int part = i;  
            executor.submit(() -> {  
                try {  
                    System.out.println("线程 " + Thread.currentThread().getName() +   
                                       " 正在处理第 " + part + " 段视频...");  
                    // 模拟任务耗时  
                    Thread.sleep((long) (Math.random() * 4000));  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                } finally {  
                    latch.countDown(); // 任务完成，倒计时减一  
                    System.out.println("线程 " + Thread.currentThread().getName() +   
                                       " 完成了第 " + part + " 段视频转码");  
                }  
            });  
        }  

        // 主线程等待所有线程完成任务，设置最长超时时间  
        boolean completed = latch.await(5, TimeUnit.SECONDS); // 最多等待 5 秒  
        executor.shutdown();  

        if (completed) {  
            System.out.println("所有视频段已完成转码，开始合成视频...");  
        } else {  
            System.out.println("部分线程任务超时，视频转码失败！");  
        }  
    }  
}  
```

#### **<font style="color:rgba(6, 8, 31, 0.88);">代码解析：</font>**
1. **<font style="color:rgba(6, 8, 31, 0.88);">计数器初始化：</font>**
    - `<font style="color:rgba(6, 8, 31, 0.88);">CountDownLatch latch = new CountDownLatch(taskCount)</font>`<font style="color:rgba(6, 8, 31, 0.88);">：初始化计数器，值为任务总数。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">倒计时逻辑：</font>**
    - <font style="color:rgba(6, 8, 31, 0.88);">在每个线程处理完分片后调用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">latch.countDown()</font>`<font style="color:rgba(6, 8, 31, 0.88);">，计数器减一。</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">主线程等待：</font>**
    - <font style="color:rgba(6, 8, 31, 0.88);">使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">latch.await(timeout, TimeUnit)</font>`<font style="color:rgba(6, 8, 31, 0.88);">，主线程设置超时时间等待所有任务完成：</font>
        * <font style="color:rgba(6, 8, 31, 0.88);">如果所有线程完成转码，则返回</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">true</font>`<font style="color:rgba(6, 8, 31, 0.88);">。</font>
        * <font style="color:rgba(6, 8, 31, 0.88);">如果超时，则返回</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">false</font>`<font style="color:rgba(6, 8, 31, 0.88);">。</font>
4. **<font style="color:rgba(6, 8, 31, 0.88);">任务超时处理：</font>**
    - <font style="color:rgba(6, 8, 31, 0.88);">如果超时未完成，主线程可以识别并处理，例如记录日志、取消任务或返回错误。</font>

<font style="color:rgba(6, 8, 31, 0.88);"></font>

### **<font style="color:rgba(6, 8, 31, 0.88);">2. 解决超时线程如何处理的问题</font>**
<font style="color:rgba(6, 8, 31, 0.88);">CountDownLatch 只能通知主线程某些任务未完成，但无法获知具体哪些线程超时。我们引入</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">CompletableFuture</font>**<font style="color:rgba(6, 8, 31, 0.88);">，实现更精细化的控制。</font>

#### **<font style="color:rgba(6, 8, 31, 0.88);">2.1 用 CompletableFuture 改进任务超时控制</font>**
**<font style="color:rgba(6, 8, 31, 0.88);">CompletableFuture</font>**<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">可以异步管理和监控每个任务的执行状态，配合</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">allOf()</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">和自定义超时策略，可以轻松实现超时处理。</font>

**<font style="color:rgba(6, 8, 31, 0.88);">代码实现：</font>**

```plain
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;
import java.util.stream.Collectors;

public class VideoTranscodingServiceWithCompletableFuture {

    public static void main(String[] args) throws InterruptedException, ExecutionException {
        int taskCount = 5; // 分片任务数量
        ExecutorService executor = Executors.newFixedThreadPool(taskCount);

        List<CompletableFuture<Boolean>> taskFutures = new ArrayList<>();

        // 启动多个分片任务
        for (int i = 1; i <= taskCount; i++) {
            int part = i;

            // 主任务：模拟视频分片转码任务
            CompletableFuture<Boolean> mainTask = CompletableFuture.supplyAsync(() -> {
                try {
                    System.out.println("线程 " + Thread.currentThread().getName() +
                            " 正在处理第 " + part + " 段视频...");
                    // 模拟任务耗时
                    Thread.sleep((long) (Math.random() * 4000)); // 随机耗时 0~4 秒
                    return true; // 返回成功
                } catch (InterruptedException e) {
                    e.printStackTrace();
                    return false; // 返回失败
                }
            }, executor);

            // 超时任务：延迟 3 秒后返回 false
            CompletableFuture<Boolean> timeoutTask = CompletableFuture.supplyAsync(() -> {
                try {
                    Thread.sleep(3000); // 超时时间为 3 秒
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                return false; // 超时返回失败
            }, executor);

            // 使用 CompletableFuture.anyOf()，主任务和超时任务竞争完成
            CompletableFuture<Boolean> taskWithTimeout = CompletableFuture.anyOf(mainTask, timeoutTask)
                    .thenApply(result -> (Boolean) result);

            taskFutures.add(taskWithTimeout);
        }

        // 等待所有任务完成
        CompletableFuture<Void> allTasks = CompletableFuture.allOf(
                taskFutures.toArray(new CompletableFuture[0])
        );

        // 结果汇总
        allTasks.thenRun(() -> {
            List<Boolean> results = taskFutures.stream()
                    .map(CompletableFuture::join)
                    .collect(Collectors.toList());
            if (results.contains(false)) {
                System.out.println("部分分片任务失败，无法合成视频！");
            } else {
                System.out.println("所有视频段转码完成，开始合成视频！");
            }
        }).join();

        executor.shutdown();
    }
}
```

#### **<font style="color:rgba(6, 8, 31, 0.88);">代码解析：</font>**
1. **<font style="color:rgba(6, 8, 31, 0.88);">异步提交任务：</font>**<font style="color:rgba(6, 8, 31, 0.88);">  
</font><font style="color:rgba(6, 8, 31, 0.88);">每个任务通过</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">CompletableFuture.supplyAsync()</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">异步提交。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">设置超时时间：</font>**
    - `<font style="color:rgba(6, 8, 31, 0.88);">future.orTimeout(3, TimeUnit.SECONDS)</font>`<font style="color:rgba(6, 8, 31, 0.88);">：针对每个任务分别设置 3 秒超时。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">超时后的任务会触发</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">exceptionally()</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">逻辑，记录超时信息且返回</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">false</font>`<font style="color:rgba(6, 8, 31, 0.88);">。</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">收集任务结果：</font>**
    - <font style="color:rgba(6, 8, 31, 0.88);">使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">CompletableFuture.allOf()</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">等待所有任务完成或超时。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">最终汇总结果，识别哪些任务完成或失败。</font>

<font style="color:rgba(6, 8, 31, 0.88);"></font>

#### **<font style="color:rgba(6, 8, 31, 0.88);">为什么选择 CompletableFuture？</font>**
1. **<font style="color:rgba(6, 8, 31, 0.88);">支持任务超时管理：</font>**<font style="color:rgba(6, 8, 31, 0.88);">  
</font><font style="color:rgba(6, 8, 31, 0.88);">可以针对每个线程独立设置超时时间和回调逻辑，精细化控制任务执行状态。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">非阻塞主线程：</font>**<font style="color:rgba(6, 8, 31, 0.88);">  
</font><font style="color:rgba(6, 8, 31, 0.88);">通过异步调用，主线程无需直接阻塞，从而提高性能。</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">内置流式处理：</font>**<font style="color:rgba(6, 8, 31, 0.88);">  
</font><font style="color:rgba(6, 8, 31, 0.88);">结合 Stream API，高效处理任务结果。</font>

<font style="color:rgba(6, 8, 31, 0.88);"></font>

### **<font style="color:rgba(6, 8, 31, 0.88);">3. CountDownLatch vs. CompletableFuture</font>**
| **<font style="color:rgba(6, 8, 31, 0.88);">特性</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">CountDownLatch</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">CompletableFuture</font>** |
| --- | --- | --- |
| **<font style="color:rgba(6, 8, 31, 0.88);">线程同步</font>** | <font style="color:rgba(6, 8, 31, 0.88);">倒计时计数器（所有线程完成时触发）</font> | <font style="color:rgba(6, 8, 31, 0.88);">使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">allOf</font>`<br/><font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">等方式同步多个任务</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">超时控制</font>** | <font style="color:rgba(6, 8, 31, 0.88);">只能设置统一的超时时间</font> | <font style="color:rgba(6, 8, 31, 0.88);">每个任务支持独立超时，异常处理灵活</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">状态追踪</font>** | <font style="color:rgba(6, 8, 31, 0.88);">无法追踪具体任务状态，仅统计完成进度</font> | <font style="color:rgba(6, 8, 31, 0.88);">可以记录并分析每个任务的结果与失败原因</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">适用场景</font>** | <font style="color:rgba(6, 8, 31, 0.88);">简单线程同步，用于较低复杂度任务管理</font> | <font style="color:rgba(6, 8, 31, 0.88);">更适合复杂、高并发任务管理（如批量 IO 操作）</font> |


### **<font style="color:rgba(6, 8, 31, 0.88);">4. 总结与实践</font>**
#### **<font style="color:rgba(6, 8, 31, 0.88);">实战场景：</font>**
+ **<font style="color:rgba(6, 8, 31, 0.88);">CountDownLatch</font>**<font style="color:rgba(6, 8, 31, 0.88);">  
</font><font style="color:rgba(6, 8, 31, 0.88);">适用于简单的同步场景，例如并发执行批量任务时，主线程需要等待所有任务完成。</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">CompletableFuture</font>**<font style="color:rgba(6, 8, 31, 0.88);">  
</font><font style="color:rgba(6, 8, 31, 0.88);">更适合高级场景，例如需分任务设置超时时间、容错处理或非阻塞操作时。</font>

#### **<font style="color:rgba(6, 8, 31, 0.88);">性能与优化建议：</font>**
1. <font style="color:rgba(6, 8, 31, 0.88);">小规模任务可以直接使用 CountDownLatch，简单可靠。</font>
2. <font style="color:rgba(6, 8, 31, 0.88);">复杂并发任务优先选择 CompletableFuture，提供更灵活的监控与管理能力。</font>
3. <font style="color:rgba(6, 8, 31, 0.88);">注意优化线程池配置，避免过多线程导致资源占用问题。</font>

<font style="color:rgba(6, 8, 31, 0.88);">在你的并发任务场景中，什么方法更适合？欢迎在评论区交流你的经验和想法！  
</font>**<font style="color:rgba(6, 8, 31, 0.88);">技术的核心是解决实际问题，愿你的代码稳如老狗！</font>**



> 更新: 2025-02-11 14:25:25  
> 原文: <https://www.yuque.com/u12222632/as5rgl/fdu6a6gblp0alrci>