# Java中如何安全地停止线程

<font style="color:rgba(0, 0, 0, 0.82);">在Java中，安全地停止线程通常涉及通过标志位来指示线程应该停止执行，而不是使用诸如</font>`<font style="color:rgba(0, 0, 0, 0.82);">Thread.stop()</font>`<font style="color:rgba(0, 0, 0, 0.82);">之类的过时且不安全的方法。直接强制终止线程可能会导致资源泄露或数据库状态不一致等问题。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用标志位停止线程</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">InterruptedException</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对于阻塞操作，使用线程的中断机制。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">标志位（Volatile）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对于正在执行的非阻塞任务，可以使用一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">volatile</font>`<font style="color:rgba(0, 0, 0, 0.82);">类型的布尔标志。</font>

<font style="color:rgba(0, 0, 0, 0.82);">以下是使用标志位和中断机制停止线程的示例：</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
```java
public class SafeThreadStop {  
    private static class WorkerThread extends Thread {  
        // 使用volatile关键字，保证多线程情况下的可见性  
        private volatile boolean running = true;  

        @Override  
        public void run() {  
            System.out.println("WorkerThread is running.");  
            try {  
                while (running && !Thread.currentThread().isInterrupted()) {  
                    // 模拟工作  
                    System.out.println("Working...");  
                    Thread.sleep(1000); // 模拟阻塞操作  
                }  
            } catch (InterruptedException e) {  
                // 如果线程被中断，则退出循环  
                System.out.println("WorkerThread interrupted.");  
            } finally {  
                cleanUp();  
            }  
        }  

        public void stopThread() {  
            running = false; // 设置标志位为false  
            interrupt();     // 中断线程以退出阻塞状态  
        }  

        // 清理工作，确保资源释放等  
        private void cleanUp() {  
            System.out.println("Cleaning up resources...");  
        }  
    }  

    public static void main(String[] args) throws InterruptedException {  
        WorkerThread worker = new WorkerThread();  
        worker.start();  

        // 运行5秒后请求停止 WorkerThread  
        Thread.sleep(5000);  
        worker.stopThread();  
        System.out.println("Requested to stop the WorkerThread.");  

        worker.join(); // 等待线程结束  
        System.out.println("WorkerThread has stopped.");  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">关键点</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">Volatile变量</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">running</font>`<font style="color:rgba(0, 0, 0, 0.82);">标志被声明为</font>`<font style="color:rgba(0, 0, 0, 0.82);">volatile</font>`<font style="color:rgba(0, 0, 0, 0.82);">，确保线程间的可见性，使得主线程修改</font>`<font style="color:rgba(0, 0, 0, 0.82);">running</font>`<font style="color:rgba(0, 0, 0, 0.82);">标志后，</font>`<font style="color:rgba(0, 0, 0, 0.82);">WorkerThread</font>`<font style="color:rgba(0, 0, 0, 0.82);">能够及时看到变化。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">线程中断</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">调用</font>`<font style="color:rgba(0, 0, 0, 0.82);">interrupt()</font>`<font style="color:rgba(0, 0, 0, 0.82);">方法可以使线程从阻塞操作（如</font>`<font style="color:rgba(0, 0, 0, 0.82);">sleep()</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">wait()</font>`<font style="color:rgba(0, 0, 0, 0.82);">等）中被中断跳出。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在执行长时间任务的循环中，也应该定期检查</font>`<font style="color:rgba(0, 0, 0, 0.82);">Thread.currentThread().isInterrupted()</font>`<font style="color:rgba(0, 0, 0, 0.82);">状态以退出循环。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">清理操作</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在可能终止的操作中包含一个</font>`<font style="color:rgba(0, 0, 0, 0.82);">finally</font>`<font style="color:rgba(0, 0, 0, 0.82);">块，用于处理任何必要的清理工作（释放资源等）。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">安全停止</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">最大限度地减少资源泄露和不一致性的风险，通过设置标志位与中断线程相结合，有序地、安全地终止线程操作。</font>

<font style="color:rgba(0, 0, 0, 0.82);">上述方法有效地结合了标志位与线程中断，显式控制线程的生命周期，实现了对线程的安全停止。</font>



> 更新: 2024-08-11 19:23:59  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/tzfdm5fio6vq3nb6>