# Netty的心跳机制怎么实现的

<font style="color:rgba(0, 0, 0, 0.82);">Netty 中的心跳机制通常用于保持客户端和服务器之间的长连接，以便在连接空闲一段时间后发送“心跳”消息来检测连接状态，避免连接意外断开。Netty 提供了一些工具和类来便捷地实现心跳机制。</font>

<font style="color:rgba(0, 0, 0, 0.82);">以下是如何在 Netty 中实现心跳机制的详细步骤和示例代码：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">步骤</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">添加 IdleStateHandler</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">Netty 提供的</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">可以检测连接的空闲状态，根据设定的时间触发相应的事件。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">实现处理心跳事件的处理器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">通过继承</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ChannelInboundHandlerAdapter</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">或</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ChannelDuplexHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);">，处理</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateEvent</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">事件，发送心跳消息或关闭连接。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">在 Pipeline 中添加处理器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：  
</font><font style="color:rgba(0, 0, 0, 0.82);">将</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和自定义的心跳处理器添加到 ChannelPipeline 中。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下示例展示了如何实现一个简单的 Netty 心跳机制：</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">服务端</font>
```java
import io.netty.bootstrap.ServerBootstrap;  
import io.netty.channel.ChannelInitializer;  
import io.netty.channel.ChannelOption;  
import io.netty.channel.ChannelPipeline;  
import io.netty.channel.EventLoopGroup;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.channel.socket.nio.NioServerSocketChannel;  
import io.netty.handler.timeout.IdleStateHandler;  

import java.util.concurrent.TimeUnit;  

public class NettyServer {  
    public static void main(String[] args) throws InterruptedException {  
        // 创建 Boss 和 Worker 线程组  
        // Boss 线程用于接收客户端的连接请求  
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);  
        // Worker 线程用于处理每个连接的 I/O 读写操作  
        EventLoopGroup workerGroup = new NioEventLoopGroup();  

        try {  
            // ServerBootstrap 用于启动 NIO 服务端  
            ServerBootstrap b = new ServerBootstrap();  
            b.group(bossGroup, workerGroup)  
            .channel(NioServerSocketChannel.class) // 指定通道类型为 NIO  
            .childHandler(new ChannelInitializer<SocketChannel>() { // 初始化每一个新连接的通道  
                @Override  
                public void initChannel(SocketChannel ch) {  
                    ChannelPipeline p = ch.pipeline();  
                    // 在管道中添加 IdleStateHandler 和自定义的心跳处理器  
                    // IdleStateHandler 参数分别表示读空闲时间、写空闲时间和读写空闲时间  
                    p.addLast(new IdleStateHandler(5, 0, 0, TimeUnit.SECONDS));  
                    // 添加自定义的处理器，处理心跳事件  
                    p.addLast(new ServerHeartbeatHandler);  
                }  
            })  
            .childOption(ChannelOption.SO_KEEPALIVE, true); // 开启 TCP Keepalive 选项  

            // 绑定端口并启动服务器  
            b.bind(8080).sync().channel().closeFuture().sync();  
        } finally {  
            // 优雅地关闭线程组，释放资源  
            workerGroup.shutdownGracefully();  
            bossGroup.shutdownGracefully();  
        }  
    }  
}  

import io.netty.channel.ChannelHandlerContext;  
import io.netty.channel.ChannelInboundHandlerAdapter;  
import io.netty.handler.timeout.IdleStateEvent;  
import io.netty.handler.timeout.IdleState;  

public class ServerHeartbeatHandler extends ChannelInboundHandlerAdapter {  
    @Override  
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {  
        // 检查是否是 IdleStateEvent 事件  
        if (evt instanceof IdleStateEvent) {  
            IdleStateEvent event = (IdleStateEvent) evt;  
            if (event.state() == IdleState.READER_IDLE) { // 读空闲事件  
                System.out.println("No read activity, sending heartbeat to client...");  
                // 发送心跳消息到客户端  
                ctx.writeAndFlush("HEARTBEAT");  
            }  
        }  
    }  

    @Override  
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {  
        cause.printStackTrace();  
        // 发生异常时关闭连接  
        ctx.close();  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">客户端</font>
```java
import io.netty.bootstrap.Bootstrap;  
import io.netty.channel.ChannelInitializer;  
import io.netty.channel.ChannelOption;  
import io.netty.channel.EventLoopGroup;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.channel.socket.nio.NioSocketChannel;  
import io.netty.handler.timeout.IdleStateHandler;  

import java.util.concurrent.TimeUnit;  

public class NettyClient {  
    public static void main(String[] args) throws InterruptedException {  
        // 创建客户端线程组，只需一个线程组处理所有连接  
        EventLoopGroup group = new NioEventLoopGroup();  

        try {  
            // Bootstrap 用于启动 NIO 客户端  
            Bootstrap b = new Bootstrap();  
            b.group(group)  
            .channel(NioSocketChannel.class) // 指定通道类型为 NIO  
            .handler(new ChannelInitializer<SocketChannel>() { // 初始化每一个新连接的通道  
                @Override  
                public void initChannel(SocketChannel ch) {  
                    // 在管道中添加 IdleStateHandler 和自定义的心跳处理器  
                    // IdleStateHandler 参数分别表示读空闲时间、写空闲时间和读写空闲时间  
                    ch.pipeline().addLast(new IdleStateHandler(0, 0, 5, TimeUnit.SECONDS));  
                    // 添加自定义的处理器，处理心跳事件  
                    ch.pipeline().addLast(new ClientHeartbeatHandler());  
                }  
            })  
            .option(ChannelOption.SO_KEEPALIVE, true); // 开启 TCP Keepalive 选项  

            // 连接服务器并等待连接关闭  
            b.connect("localhost", 8080).sync().channel().closeFuture().sync();  
        } finally {  
            // 优雅地关闭线程组，释放资源  
            group.shutdownGracefully();  
        }  
    }  
}  

import io.netty.channel.ChannelHandlerContext;  
import io.netty.channel.ChannelInboundHandlerAdapter;  
import io.netty.handler.timeout.IdleStateEvent;  
import io.netty.handler.timeout.IdleState;  

public class ClientHeartbeatHandler extends ChannelInboundHandlerAdapter {  
    @Override  
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {  
        // 检查是否是 IdleStateEvent 事件  
        if (evt instanceof IdleStateEvent) {  
            IdleStateEvent event = (IdleStateEvent) evt;  
            if (event.state() == IdleState.ALL_IDLE) { // 读写空闲事件  
                System.out.println("No write activity, sending heartbeat to server...");  
                // 发送心跳消息到服务器  
                ctx.writeAndFlush("HEARTBEAT");  
            }  
        }  
    }  

    @Override  
    public void channelRead(ChannelHandlerContext ctx, Object msg) {  
        // 接收并处理服务器发送的消息  
        System.out.println("Received message: " + msg);  
    }  

    @Override  
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {  
        cause.printStackTrace();  
        // 发生异常时关闭连接  
        ctx.close();  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">代码解释</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">服务端代码解释</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ServerBootstrap</font>`<font style="color:rgba(0, 0, 0, 0.82);">：配置和启动 NIO 服务端。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">NioServerSocketChannel</font>`<font style="color:rgba(0, 0, 0, 0.82);">：指定服务端的通道类型为 NIO。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);">：添加到 Pipeline 中，检测连接的空闲状态。这段代码中配置了</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">READ_IDLE</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">5 秒，即连接 5 秒未读取数据时触发空闲事件。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ServerHeartbeatHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);">：自定义的处理器，处理空闲事件（心跳检测）。在发生读空闲事件时，发送心跳消息“HEARTBEAT”给客户端。遇到异常时关闭连接。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">客户端代码解释</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">Bootstrap</font>`<font style="color:rgba(0, 0, 0, 0.82);">：配置和启动 NIO 客户端。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">NioSocketChannel</font>`<font style="color:rgba(0, 0, 0, 0.82);">：指定客户端的通道类型为 NIO。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);">：添加到 Pipeline 中，检测连接的空闲状态。配置了</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ALL_IDLE</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">5 秒，即连接 5 秒既未读取也未写入数据时触发空闲事件。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">ClientHeartbeatHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);">：自定义的处理器，处理空闲事件（心跳检测）。在发生读写空闲事件时，发送心跳消息“HEARTBEAT”给服务器。处理服务器的心跳响应消息并打印。遇到异常时关闭连接。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过以上代码示例和详细注释，您可以更好地理解 Netty 中心跳机制的实现方式。Netty 提供的 </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 非常便利，能够有效简化心跳检测的实现，确保连接在长时间闲置时保持活跃状态。</font>



> 更新: 2024-08-05 20:36:56  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/pmr9xbb2e85445xs>