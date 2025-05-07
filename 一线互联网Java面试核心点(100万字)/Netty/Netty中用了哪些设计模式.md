# Netty中用了哪些设计模式

<font style="color:rgba(0, 0, 0, 0.82);">Netty 作为一个高性能、异步事件驱动的网络框架，其实现中运用了多种设计模式，以提高代码复用性、可维护性以及系统的灵活性和可扩展性。下面列出了一些 Netty 中主要的设计模式及其实现：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 责任链模式（Chain of Responsibility）</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty 的 ChannelPipeline 和 ChannelHandler 是责任链模式的典型实现。所有的 ChannelHandler 都链接在一个链中，每个 Handler 处理自己的部分，然后将事件传递到下一个 Handler。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
ChannelPipeline pipeline = channel.pipeline();  
pipeline.addLast(new LoggingHandler(LogLevel.INFO));  
pipeline.addLast(new HttpRequestDecoder());  
pipeline.addLast(new HttpResponseEncoder());  
pipeline.addLast(new MyBusinessLogicHandler());
```

### <font style="color:rgba(0, 0, 0, 0.82);">2. 观察者模式（Observer Pattern）</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty 的 Future 和 ChannelFutureListener 功能实现了观察者模式。当异步操作完成时，Future 通知所有的注册监听器。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
ChannelFuture future = channel.writeAndFlush(msg);  
future.addListener(new ChannelFutureListener() {  
    @Override  
    public void operationComplete(ChannelFuture future) {  
        if (future.isSuccess()) {  
            System.out.println("Write successful");  
        } else {  
            System.err.println("Write failed");  
            future.cause().printStackTrace();  
        }  
    }  
});
```

### <font style="color:rgba(0, 0, 0, 0.82);">3. Reactor 模式（Reactor Pattern）</font>
<font style="color:rgba(0, 0, 0, 0.82);">Reactor 模式是 Netty 的核心，用于处理和分发 I/O 事件。Netty 的 EventLoopGroup 和 Channel 是这一模式的具体实现。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
EventLoopGroup bossGroup = new NioEventLoopGroup();  
EventLoopGroup workerGroup = new NioEventLoopGroup();  
ServerBootstrap b = new ServerBootstrap();  
b.group(bossGroup, workerGroup)  
.channel(NioServerSocketChannel.class)  
.childHandler(new ChannelInitializer<SocketChannel>() {  
    @Override  
    protected void initChannel(SocketChannel ch) {  
        ch.pipeline().addLast(new MyServerHandler());  
    }  
});
```

### <font style="color:rgba(0, 0, 0, 0.82);">4. 工厂模式（Factory Pattern）</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty 使用工厂模式来创建不同的 Channel 和 EventLoop 实例。例如，</font>`<font style="color:rgba(0, 0, 0, 0.82);">NioServerSocketChannel</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">NioEventLoopGroup</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">都是实现了相应接口的具体工厂类。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
EventLoopGroup group = new NioEventLoopGroup();  
ServerBootstrap bootstrap = new ServerBootstrap();  
bootstrap.group(group)  
.channel(NioServerSocketChannel.class)  
.childHandler(new ChannelInitializer<SocketChannel>() {  
    @Override  
    protected void initChannel(SocketChannel ch) {  
        ch.pipeline().addLast(new MyServerHandler());  
    }  
});
```

### <font style="color:rgba(0, 0, 0, 0.82);">5. 模板方法模式（Template Method Pattern）</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty 中的</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ChannelInitializer</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">类使用了模板方法模式。开发者可以通过继承</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ChannelInitializer</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">类，并实现</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">initChannel</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法来配置自定义的 ChannelPipeline。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
public class MyChannelInitializer extends ChannelInitializer<SocketChannel> {  
    @Override  
    protected void initChannel(SocketChannel ch) {  
        ch.pipeline().addLast(new MyHandler());  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">6. 单例模式（Singleton Pattern）</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty 中的一些核心组件，如</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">PooledByteBufAllocator</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">使用了单例模式，以确保全局范围内只存在一个实例，并且可以高效地进行内存分配。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
ByteBufAllocator allocator = PooledByteBufAllocator.DEFAULT;  
ByteBuf buffer = allocator.buffer();
```

### <font style="color:rgba(0, 0, 0, 0.82);">7. 装饰者模式（Decorator Pattern）</font>
<font style="color:rgba(0, 0, 0, 0.82);">ChannelHandler 的装饰链实际上也是装饰者模式的一个典型实现。每个 ChannelHandler 可以在处理数据之前或之后添加一些附加的功能，而不用修改其他的处理器。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```java
ChannelPipeline pipeline = channel.pipeline();  
pipeline.addLast("decoder", new MyDecoder());  
pipeline.addLast("encoder", new MyEncoder());  
pipeline.addLast("handler", new MyBusinessLogicHandler());
```

<font style="color:rgba(0, 0, 0, 0.82);">这些设计模式在 Netty 中被有机结合使用，使得 Netty 成为一个功能强大、灵活且高性能的网络通信框架。通过这些设计模式，Netty 能够更好地应对不同的应用场景和需求，提高开发效率与代码质量。</font>



> 更新: 2024-08-05 20:39:28  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/yhaq1qke0gp6lf9l>