# BIO、NIO、AIO有什么区别

他们三者都是Java中常用的I/O模型，我们从以下三个维度进行对比：

1. **阻塞与非阻塞：**
    - BIO是阻塞式I/O模型，线程会一直被阻塞等待操作完成。
    - NIO是非阻塞式I/O模型，线程可以去做其他任务，当I/O操作完成时得到通知。
    - AIO也是非阻塞式I/O模型，不需要用户线程关注I/O事件，由操作系统通过回调机制处理。
2. **缓冲区：**
    - BIO使用传统的字节流和字符流，需要为输入输出流分别创建缓冲区。
    - NIO引入了基于通道和缓冲区的I/O方式，使用一个缓冲区完成数据读写操作。
    - AIO则不需要缓冲区，使用异步回调方式进行操作。
3. **线程模型：**
    - BIO采用一个线程处理一个请求方式，面对高并发时线程数量急剧增加，容易导致系统崩溃。
    - NIO采用多路复用器来监听多个客户端请求，使用一个线程处理，减少线程数量，提高系统性能。
    - AIO依靠操作系统完成I/O操作，不需要额外的线程池或多路复用器。

综上所述，BIO、NIO、AIO的区别主要在于阻塞与非阻塞、缓冲区和线程模型等方面。根据具体应用场景选择合适的I/O模型可以提高程序的性能和可扩展性。



> 更新: 2023-09-06 14:07:49  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dpxl834qsx20hfn3>