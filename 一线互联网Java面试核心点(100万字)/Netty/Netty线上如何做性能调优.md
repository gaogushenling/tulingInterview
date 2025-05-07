# Netty线上如何做性能调优

<font style="color:rgba(0, 0, 0, 0.82);">进行 Netty 程序的性能调优，可以从多个方面入手，包括线程模型、内存管理、数据压缩和连接管理等。以下是一些关键的性能调优策略和常见的坑：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 优化线程模型</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">调优策略：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">调整线程池的大小</font>**<font style="color:rgba(0, 0, 0, 0.82);">：要确保线程池大小（</font>`<font style="color:rgba(0, 0, 0, 0.82);">NioEventLoopGroup</font>`<font style="color:rgba(0, 0, 0, 0.82);">）适合你的应用程序。通常，线程池大小为 CPU 核心数的 2 倍。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">分开业务逻辑和 IO 线程</font>**<font style="color:rgba(0, 0, 0, 0.82);">：将业务逻辑处理从 IO 处理线程池分离出来，避免阻塞 IO 线程。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>**

```java
int bossGroupSize = Runtime.getRuntime().availableProcessors();  
int workerGroupSize = bossGroupSize * 2;  

EventLoopGroup bossGroup = new NioEventLoopGroup(bossGroupSize);  
EventLoopGroup workerGroup = new NioEventLoopGroup(workerGroupSize);
```

#### <font style="color:rgba(0, 0, 0, 0.82);">常见的坑：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">线程池大小设置不当</font>**<font style="color:rgba(0, 0, 0, 0.82);">：过大或过小的线程池会影响性能，导致 CPU 负载不均或过度切换。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">阻塞 IO 线程</font>**<font style="color:rgba(0, 0, 0, 0.82);">：避免在 IO 线程中执行耗时操作，如数据库查询或文件 I/O。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. 内存管理</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">调优策略：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">使用池化内存分配</font>**<font style="color:rgba(0, 0, 0, 0.82);">：启用 PooledByteBufAllocator 以提高内存分配效率。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">减少对象创建和销毁</font>**<font style="color:rgba(0, 0, 0, 0.82);">：尽量重用对象，减少垃圾回收的频率。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>**

```java
ServerBootstrap b = new ServerBootstrap();  
b.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
```

#### <font style="color:rgba(0, 0, 0, 0.82);">常见的坑：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">内存泄漏</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确保 ByteBuf 被正确释放，避免内存泄漏。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">频繁的垃圾回收</font>**<font style="color:rgba(0, 0, 0, 0.82);">：监控 GC 日志，避免频繁的 Full GC。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">3. 数据压缩和编解码</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">调优策略：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">使用合适的编解码器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：选择高效的编解码器，如 Protobuf、JSON 等。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">数据压缩</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对大数据块进行压缩，减少网络带宽消耗。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>**

```java
pipeline.addLast("compressor", new JdkZlibEncoder(ZlibWrapper.GZIP));  
pipeline.addLast("decompressor", new JdkZlibDecoder(ZlibWrapper.GZIP));
```

#### <font style="color:rgba(0, 0, 0, 0.82);">常见的坑：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">选择不当的编解码器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：不适合的数据格式会增加序列化和反序列化的开销。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">过度压缩</font>**<font style="color:rgba(0, 0, 0, 0.82);">：压缩和解压缩会消耗 CPU 资源，权衡压缩率和 CPU 开销。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">4. 连接管理</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">调优策略：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">保持连接活跃</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用心跳检测机制确保连接的长久性。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">连接池化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：对于客户端，使用连接池来提高连接复用率。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>**

```java
pipeline.addLast("idleStateHandler", new IdleStateHandler(60, 30, 0));  
pipeline.addLast("heartbeatHandler", new HeartbeatHandler());
```

#### <font style="color:rgba(0, 0, 0, 0.82);">常见的坑：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">未处理的连接断开</font>**<font style="color:rgba(0, 0, 0, 0.82);">：确保处理好连接断开和重新连接逻辑。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">5. 调整 TCP 参数</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">调优策略：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">调整 TCP 缓冲区大小</font>**<font style="color:rgba(0, 0, 0, 0.82);">：根据网络带宽和延迟，适当调整</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">SO_RCVBUF</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">SO_SNDBUF</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">启用 TCP_NODELAY</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在延迟敏感的场景下(比如实时聊天，金融融交易实时数据)，启用 TCP_NODELAY 以禁用 Nagle 算法(Nagle 算法会导致小数据包被缓存，直到缓存区数据量达到一定大小后才会发送)。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>**

```java
b.childOption(ChannelOption.SO_RCVBUF, 1024 * 1024);  
b.childOption(ChannelOption.SO_SNDBUF, 1024 * 1024);  
b.childOption(ChannelOption.TCP_NODELAY, true);
```

#### <font style="color:rgba(0, 0, 0, 0.82);">常见的坑：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">错误的缓冲区设置</font>**<font style="color:rgba(0, 0, 0, 0.82);">：过大的缓冲区会增加内存消耗，过小的缓冲区会导致频繁的发送和接收。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">误用 TCP_NODELAY</font>**<font style="color:rgba(0, 0, 0, 0.82);">：不在意延迟的场景下，不启用 TCP_NODELAY 以避免额外的 CPU 开销。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);"></font>
#### 
### <font style="color:rgba(0, 0, 0, 0.82);">6. 使用日志和监控</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">调优策略：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">使用日志和监控工具</font>**<font style="color:rgba(0, 0, 0, 0.82);">：借助工具如 JMX、Grafana 和 Prometheus 监控应用性能。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">配置合适的日志级别</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在生产环境下，避免过度的日志写操作。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">示例代码：</font>**

```java
pipeline.addLast("logger", new LoggingHandler(LogLevel.INFO));
```

#### <font style="color:rgba(0, 0, 0, 0.82);">常见的坑：</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">忽略监控数据</font>**<font style="color:rgba(0, 0, 0, 0.82);">：未能及时发现和处理性能瓶颈。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">过多的日志</font>**<font style="color:rgba(0, 0, 0, 0.82);">：日志过多会影响应用性能。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">综合示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下是一个综合的 ServerBootstrap 初始化示例，结合了多项调优策略：</font>

```java
EventLoopGroup bossGroup = new NioEventLoopGroup(Runtime.getRuntime().availableProcessors());  
EventLoopGroup workerGroup = new NioEventLoopGroup();  
try {  
    ServerBootstrap b = new ServerBootstrap();  
    b.group(bossGroup, workerGroup)  
    .channel(NioServerSocketChannel.class)  
    .option(ChannelOption.SO_BACKLOG, 1024)  
    .option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT)  
    .childOption(ChannelOption.SO_RCVBUF, 1024 * 1024)  
    .childOption(ChannelOption.SO_SNDBUF, 1024 * 1024)  
    .childOption(ChannelOption.TCP_NODELAY, true)  
    .childHandler(new ChannelInitializer<SocketChannel>() {  
        @Override  
        protected void initChannel(SocketChannel ch) throws Exception {  
            ChannelPipeline pipeline = ch.pipeline();  
            pipeline.addLast("idleStateHandler", new IdleStateHandler(60, 30, 0));  
            pipeline.addLast("compressor", new JdkZlibEncoder(ZlibWrapper.GZIP));  
            pipeline.addLast("decompressor", new JdkZlibDecoder(ZlibWrapper.GZIP));  
            pipeline.addLast("businessLogicHandler", new MyBusinessLogicHandler());  
        }  
    });  

    ChannelFuture f = b.bind(8080).sync();  
    f.channel().closeFuture().sync();  
} finally {  
    workerGroup.shutdownGracefully();  
    bossGroup.shutdownGracefully();  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">通过针对 Netty 的特定需求，合理使用这些调优策略，可以显著提升 Netty 应用程序的性能和可靠性。同时，通过监控与日志分析，及时发现并解决问题，避免常见的坑。</font>

### <font style="color:rgba(0, 0, 0, 0.82);"></font>
```java

```



> 更新: 2024-08-05 20:41:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ihqntb112grs4ag2>