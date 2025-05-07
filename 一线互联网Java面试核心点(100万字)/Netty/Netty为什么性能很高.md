# Netty为什么性能很高

<font style="color:rgba(0, 0, 0, 0.82);">Netty 之所以性能很高，主要归功于以下几个方面：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">基于 NIO 的非阻塞 I/O 模型</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Netty 使用 Java NIO 实现非阻塞 I/O，一个线程可以管理多个连接，高效处理 I/O 操作。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">事件驱动模型（Reactor 模式）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Netty 采用事件驱动的异步非阻塞模型，减少了线程上下文切换开销，提高了响应速度。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">内存池化技术</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过内存池复用 ByteBuf，减少了频繁的内存分配和回收(GC)带来的性能开销。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">零拷贝技术</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用 DirectBuffer 和 FileChannel 实现零拷贝，减少内存复制，提高数据传输效率。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">高效的线程管理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Netty 的线程模型通过 EventLoopGroup 高效管理线程，充分利用多核 CPU。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">高效的 Pipeline 机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Netty 的 Pipeline 机制使处理链非常灵活，可插拔各种 Handler 进行数据处理。</font>

<font style="color:rgba(0, 0, 0, 0.82);">以下是一个使用 Netty 构建的高性能服务器的示例代码，该代码演示了如何利用 Netty 的上述高性能特性：</font>

```java
import io.netty.bootstrap.ServerBootstrap;  
import io.netty.buffer.ByteBuf;  
import io.netty.buffer.PooledByteBufAllocator;  
import io.netty.channel.ChannelFuture;  
import io.netty.channel.ChannelHandlerContext;  
import io.netty.channel.ChannelInboundHandlerAdapter;  
import io.netty.channel.EventLoopGroup;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.channel.socket.nio.NioServerSocketChannel;  
import io.netty.channel.ChannelInitializer;  
import io.netty.util.CharsetUtil;  

public class NettyServer {  
    public static void main(String[] args) throws InterruptedException {  
        // 创建 boss 和 worker 线程组  
        EventLoopGroup bossGroup = new NioEventLoopGroup(1); // boss 线程, 处理 Accept 事件  
        EventLoopGroup workerGroup = new NioEventLoopGroup(Runtime.getRuntime().availableProcessors() * 2); // worker 线程组, 处理读写事件  

        try {  
            // 创建 ServerBootstrap, 用于配置和启动服务器  
            ServerBootstrap b = new ServerBootstrap();  
            b.group(bossGroup, workerGroup)  
            .channel(NioServerSocketChannel.class)  
            .childHandler(new ChannelInitializer<SocketChannel>() {  
                @Override  
                public void initChannel(SocketChannel ch) {  
                    // 使用 Pipeline 机制, 配置 Handler  
                    ch.pipeline().addLast(new NettyServerHandler());  
                }  
            })  
            .option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT) // 使用 Pooled ByteBuf  
            .childOption(ChannelOption.SO_KEEPALIVE, true); // 启用 TCP 连接保活机制  

            // 绑定端口, 启动服务器  
            ChannelFuture f = b.bind(8080).sync();  
            System.out.println("Server started on port 8080.");  

            // 等待服务器关闭  
            f.channel().closeFuture().sync();  
        } finally {  
            // 优雅关闭线程组  
            workerGroup.shutdownGracefully();  
            bossGroup.shutdownGracefully();  
        }  
    }  

    private static class NettyServerHandler extends ChannelInboundHandlerAdapter {  
        @Override  
        public void channelRead(ChannelHandlerContext ctx, Object msg) {  
            ByteBuf in = (ByteBuf) msg;  
            System.out.println("Server received: " + in.toString(CharsetUtil.UTF_8));  
            ctx.write(in); // 回显数据, 实现简单的 Echo 服务  
        }  

        @Override  
        public void channelReadComplete(ChannelHandlerContext ctx) {  
            ctx.flush(); // 刷新数据到客户端  
        }  

        @Override  
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {  
            cause.printStackTrace();  
            ctx.close(); // 发生异常时关闭连接  
        }  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">代码解释</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">线程模型</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">bossGroup</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">workerGroup</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">是两个 NioEventLoopGroup，分别处理连接建立和 I/O 读写。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">NioEventLoopGroup</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">的线程数设置为</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">Runtime.getRuntime().availableProcessors() * 2</font>`<font style="color:rgba(0, 0, 0, 0.82);">，充分利用多核 CPU 性能。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">Channel 和 Pipeline</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ServerBootstrap</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">配置服务器启动。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">NioServerSocketChannel</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">代表 NIO 服务端套接字通道。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ChannelInitializer</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">用于在新连接建立时初始化</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">SocketChannel</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">NettyServerHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">是自定义的处理器，用于处理 I/O 事件。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">内存池化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">b.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT)</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">使用池化的 ByteBuf 分配器，减少内存分配开销。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">事件驱动</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ctx.write(in)</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ctx.flush()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">实现异步的 I/O 操作，提高响应速度。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">异常处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">exceptionCaught</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法中处理异常，并在发生异常时关闭连接，确保系统健壮性。</font>
6. **<font style="color:rgba(0, 0, 0, 0.82);">零拷贝</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">Netty 内部的零拷贝并未在这个简单的示例中直接展示，但在内部实现中使用了 DirectBuffer 等技术来减少内存复制。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些设计和实现，Netty 能够实现高性能、低延迟的网络通信，适用于各种高并发场景。</font>



> 更新: 2024-08-05 20:35:29  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ylxm2g3tzebbcbhc>