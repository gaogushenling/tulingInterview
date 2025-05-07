# Netty如何处理闲置连接

<font style="color:rgba(0, 0, 0, 0.82);">在 Netty 中，比如一个客户端连接长时间没有发送数据，这种闲置连接（idle connection）该如何监测并处理？Netty是通过 </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 来处理闲置连接的。</font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 是一个 ChannelHandler，用于检测读、写或读写的空闲状态，并在空闲状态发生时触发 </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateEvent</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 事件。你可以通过捕获和处理这些事件来执行相应的操作，比如关闭闲置连接。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">处理闲置连接</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下是如何使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">处理闲置连接的步骤：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">添加</font>****<font style="color:rgba(0, 0, 0, 0.82);"> </font>**`**<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>**`**<font style="color:rgba(0, 0, 0, 0.82);"> </font>****<font style="color:rgba(0, 0, 0, 0.82);">到 ChannelPipeline</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">的构造函数接受三个参数：读空闲时间、写空闲时间和读写空闲时间。你可以根据需要设置这些参数。</font>

2. **<font style="color:rgba(0, 0, 0, 0.82);">捕获</font>****<font style="color:rgba(0, 0, 0, 0.82);"> </font>**`**<font style="color:rgba(0, 0, 0, 0.82);">IdleStateEvent</font>**`**<font style="color:rgba(0, 0, 0, 0.82);"> </font>****<font style="color:rgba(0, 0, 0, 0.82);">事件</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

<font style="color:rgba(0, 0, 0, 0.82);">创建一个自定义的 ChannelInboundHandler 来捕获</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateEvent</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">事件，并在事件发生时执行相应的处理逻辑。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">代码示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下是一个完整的示例，展示了如何在 Netty 中使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">处理闲置连接：</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">服务器端代码</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">自定义处理器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
import io.netty.channel.ChannelHandlerContext;  
import io.netty.channel.ChannelInboundHandlerAdapter;  
import io.netty.handler.timeout.IdleState;  
import io.netty.handler.timeout.IdleStateEvent;  

public class MyIdleStateHandler extends ChannelInboundHandlerAdapter {  
    @Override  
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {  
        if (evt instanceof IdleStateEvent) {  
            IdleStateEvent event = (IdleStateEvent) evt;  
            if (event.state() == IdleState.READER_IDLE) {  
                System.out.println("读空闲超时，关闭连接");  
                ctx.close();  
            } else if (event.state() == IdleState.WRITER_IDLE) {  
                System.out.println("写空闲超时");  
            } else if (event.state() == IdleState.ALL_IDLE) {  
                System.out.println("读写空闲超时");  
            }  
        } else {  
            super.userEventTriggered(ctx, evt);  
        }  
    }  
}
```

1. **<font style="color:rgba(0, 0, 0, 0.82);">初始化 ChannelPipeline</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
import io.netty.channel.ChannelInitializer;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.handler.timeout.IdleStateHandler;  

public class MyChannelInitializer extends ChannelInitializer<SocketChannel> {  
    @Override  
    protected void initChannel(SocketChannel ch) throws Exception {  
        ch.pipeline().addLast(new IdleStateHandler(60, 30, 0)); // 读超时60秒，写超时30秒  
        ch.pipeline().addLast(new MyIdleStateHandler());  
    }  
}
```

1. **<font style="color:rgba(0, 0, 0, 0.82);">启动服务器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
import io.netty.bootstrap.ServerBootstrap;  
import io.netty.channel.ChannelFuture;  
import io.netty.channel.ChannelOption;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.nio.NioServerSocketChannel;  

public class NettyServer {  
    public static void main(String[] args) throws Exception {  
        NioEventLoopGroup bossGroup = new NioEventLoopGroup();  
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();  
        try {  
            ServerBootstrap b = new ServerBootstrap();  
            b.group(bossGroup, workerGroup)  
            .channel(NioServerSocketChannel.class)  
            .childHandler(new MyChannelInitializer())  
            .option(ChannelOption.SO_BACKLOG, 128)  
            .childOption(ChannelOption.SO_KEEPALIVE, true);  

            // 绑定端口，开始接收进来的连接  
            ChannelFuture f = b.bind(8080).sync();  

            // 等待服务器 socket 关闭  
            f.channel().closeFuture().sync();  
        } finally {  
            workerGroup.shutdownGracefully();  
            bossGroup.shutdownGracefully();  
        }  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">客户端代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">客户端代码类似于服务器端，只需要在客户端的 ChannelPipeline 中添加</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和自定义的处理器即可。</font>

```java
import io.netty.bootstrap.Bootstrap;  
import io.netty.channel.ChannelInitializer;  
import io.netty.channel.ChannelOption;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.channel.socket.nio.NioSocketChannel;  
import io.netty.handler.timeout.IdleStateHandler;  

public class NettyClient {  
    public static void main(String[] args) throws Exception {  
        NioEventLoopGroup group = new NioEventLoopGroup();  
        try {  
            Bootstrap b = new Bootstrap();  
            b.group(group)  
            .channel(NioSocketChannel.class)  
            .option(ChannelOption.SO_KEEPALIVE, true)  
            .handler(new ChannelInitializer<SocketChannel>() {  
                @Override  
                public void initChannel(SocketChannel ch) throws Exception {  
                    ch.pipeline().addLast(new IdleStateHandler(0, 30, 0)); // 仅设置写超时  
                    ch.pipeline().addLast(new MyIdleStateHandler());  
                }  
            });  

            // 启动客户端  
            ChannelFuture f = b.connect("localhost", 8080).sync();  

            // 等待连接关闭  
            f.channel().closeFuture().sync();  
        } finally {  
            group.shutdownGracefully();  
        }  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">IdleStateHandler</font>**<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">是 Netty 提供的处理闲置连接的工具。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">通过在 ChannelPipeline 中添加 IdleStateHandler，可以检测读、写或读写空闲事件。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">自定义处理器可以捕获 IdleStateEvent 事件，并执行相应的处理逻辑，如关闭闲置连接。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">配置 IdleStateHandler 的读、写和读写空闲时间，可以灵活地应对不同的应用场景。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些步骤，Netty 可以有效地监测并处理闲置连接，确保资源的合理利用和系统的稳定运行。</font>



> 更新: 2024-08-05 20:38:24  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/svyiga5kp5glyb5o>