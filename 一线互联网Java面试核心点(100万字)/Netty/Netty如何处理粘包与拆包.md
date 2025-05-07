# Netty如何处理粘包与拆包

<font style="color:rgba(0, 0, 0, 0.82);">在网络编程中，粘包和拆包是常见的问题，特别是在使用TCP协议进行通讯时。Netty作为一个高性能的网络框架，提供了多种方法来处理粘包和拆包问题。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">什么是粘包和拆包</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">粘包</font>**<font style="color:rgba(0, 0, 0, 0.82);">：指的是发送方将几段数据连续发送到网络中，接收方将若干段数据粘合在一起作为一次接收到的数据。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">拆包</font>**<font style="color:rgba(0, 0, 0, 0.82);">：指的是发送方一次性发送的数据由于某种原因被分成了多次发送，接收方在接收时将这些数据分成了若干次接收。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">处理粘包和拆包的方法</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty提供了一系列的</font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteToMessageDecoder</font>`<font style="color:rgba(0, 0, 0, 0.82);">的具体实现类来解决粘包和拆包问题，包括但不限于以下几种方法：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">固定长度的帧解码器（FixedLengthFrameDecoder）</font>**

<font style="color:rgba(0, 0, 0, 0.82);">这种方法适用于消息长度固定的场景。解码器会按照指定的长度来截取数据，从而避免粘包和拆包问题。</font>

```java
ChannelPipeline pipeline = ...  
pipeline.addLast(new FixedLengthFrameDecoder(10)); // 每个帧长度为10字节
```

2. **<font style="color:rgba(0, 0, 0, 0.82);">行分隔符解码器（LineBasedFrameDecoder）</font>**

<font style="color:rgba(0, 0, 0, 0.82);">这种方法适用于以特定字符（如换行符）为分隔符的场景。解码器会在检测到分隔符时将数据截取出来。</font>

```java
ChannelPipeline pipeline = ...  
pipeline.addLast(new LineBasedFrameDecoder(1024)); // 设置单行最大长度为1024，如果超过这个长度且没有找到分隔符，将抛出 TooLongFrameException。
```

3. **<font style="color:rgba(0, 0, 0, 0.82);">分隔符解码器（DelimiterBasedFrameDecoder）</font>**

<font style="color:rgba(0, 0, 0, 0.82);">这种方法适用于使用特定分隔符来标志消息边界的场景。可以自定义分隔符，如换行符、空格等。</font>

```java
ChannelPipeline pipeline = ...  
ByteBuf delimiter = Unpooled.copiedBuffer("||".getBytes());  
pipeline.addLast(new DelimiterBasedFrameDecoder(1024, delimiter));
```

4. **<font style="color:rgba(0, 0, 0, 0.82);">基于长度的帧解码器（LengthFieldBasedFrameDecoder）</font>**

<font style="color:rgba(0, 0, 0, 0.82);">这种方法适用于消息包含长度字段的场景。解码器通过读取长度字段的值来确定每个消息的边界。</font>

```java
ChannelPipeline pipeline = ...  
pipeline.addLast(new LengthFieldBasedFrameDecoder(  
    1024, 0, 4, 0, 4)); // maxFrameLength, lengthFieldOffset, lengthFieldLength, lengthAdjustment, initialBytesToStrip
//maxFrameLength：消息帧的最大长度。如果接收到的消息帧长度超过这个值，将抛出 TooLongFrameException。
//lengthFieldOffset：长度字段的偏移量，即长度字段在消息中的起始位置。
//lengthFieldLength：长度字段的字节数，常见的值有 1、2、4、8。
//lengthAdjustment：长度调整值，用于修正长度字段的值。如果长度字段包含了消息头的长度，则需要减去消息头的长度。
//initialBytesToStrip：解码后需要跳过的字节数，通常用于跳过消息头。

```

### <font style="color:rgba(0, 0, 0, 0.82);">具体示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">下面是一个示例，展示了如何使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">LengthFieldBasedFrameDecoder</font>`<font style="color:rgba(0, 0, 0, 0.82);">和自定义解码器来处理粘包和拆包问题。</font>

<font style="color:rgba(0, 0, 0, 0.82);">假设我们定义了一种协议，每条消息的前4个字节表示消息的长度，接下来的字节表示实际的消息内容。</font>

#### <font style="color:rgba(0, 0, 0, 0.82);">编码器和解码器</font>
```java
// 解码器  
public class CustomDecoder extends ByteToMessageDecoder {  
    @Override  
    protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {  
        if (in.readableBytes() < 4) {  
            return; // 长度字段还没接收到  
        }  

        in.markReaderIndex();  
        int length = in.readInt(); // 读取长度字段  
        
        byte[] bytes = new byte[length];  
        in.readBytes(bytes);  

        String message = new String(bytes, StandardCharsets.UTF_8);  
        out.add(message); // 解码后的消息添加到输出列表  
    }  
}  

// 编码器  
public class CustomEncoder extends MessageToByteEncoder<String> {  
    @Override  
    protected void encode(ChannelHandlerContext ctx, String msg, ByteBuf out) throws Exception {  
        byte[] bytes = msg.getBytes(StandardCharsets.UTF_8);  
        int length = bytes.length;  

        out.writeInt(length);   // 写入长度字段  
        out.writeBytes(bytes);  // 写入内容  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">设置ChannelPipeline</font>
```java
public class MyChannelInitializer extends ChannelInitializer<SocketChannel> {  
    @Override  
    protected void initChannel(SocketChannel ch) throws Exception {  
        ChannelPipeline pipeline = ch.pipeline();  
        pipeline.addLast(new LengthFieldBasedFrameDecoder(1024, 0, 4, 0, 4));  
        pipeline.addLast(new CustomDecoder());  
        pipeline.addLast(new CustomEncoder());  
        pipeline.addLast(new MyBusinessLogicHandler());  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Netty 提供了一系列的解码器，如FixedLengthFrameDecoder、LineBasedFrameDecoder、DelimiterBasedFrameDecoder和LengthFieldBasedFrameDecoder，以处理粘包和拆包问题。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">你可以根据你的具体需求选择合适的解码器，也可以自定义自己的解码器。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">通过合理配置ChannelPipeline，可以高效地解决TCP通信中的粘包和拆包问题。</font>

<font style="color:rgba(0, 0, 0, 0.82);">这些处理方法结合了Netty强大的内存管理和高效的网络IO，使得Netty在处理高并发网络通信时表现出色。</font>



> 更新: 2024-08-05 20:37:51  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rexk3p7tkufftatu>