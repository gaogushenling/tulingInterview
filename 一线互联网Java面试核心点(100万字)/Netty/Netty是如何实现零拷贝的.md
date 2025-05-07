# Netty是如何实现零拷贝的

<font style="color:rgba(0, 0, 0, 0.82);">零拷贝（Zero-Copy）是一种优化技术，旨在减少或完全消除数据在内存中的复制过程，从而提高系统的性能和效率。在传统的数据传输过程中，数据可能会被多次复制，例如从磁盘到内核缓冲区，然后从内核缓冲区复制到用户缓冲区，再从用户缓冲区复制到另一块内存。这些多次复制不但浪费了 CPU 资源，还增加了内存带宽的使用。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">零拷贝的工作原理</font>
<font style="color:rgba(0, 0, 0, 0.82);">在实现零拷贝的过程中，数据不会在内存中被多次复制，而是直接从一个地址移动到另一个地址。常见的零拷贝技术包括：</font>

1. `**<font style="color:rgba(0, 0, 0, 0.82);">sendfile</font>**`**<font style="color:rgba(0, 0, 0, 0.82);"> </font>****<font style="color:rgba(0, 0, 0, 0.82);">系统调用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：直接在内核空间中将文件数据发送到网络 socket。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">内存映射文件（Memory-Mapped Files）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">mmap</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">系统调用将文件映射到内存空间，然后可以直接操作内存中的数据。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">DMA（直接内存访问）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：硬件级别的技术，允许设备直接访问主存而不需要通过 CPU。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">Netty 中如何实现零拷贝</font>
<font style="color:rgba(0, 0, 0, 0.82);">Netty 通过多种机制来实现零拷贝，具体如下：</font>

1. `**<font style="color:rgba(0, 0, 0, 0.82);">FileRegion</font>**`**<font style="color:rgba(0, 0, 0, 0.82);"> </font>****<font style="color:rgba(0, 0, 0, 0.82);">类和</font>****<font style="color:rgba(0, 0, 0, 0.82);"> </font>**`**<font style="color:rgba(0, 0, 0, 0.82);">sendfile</font>**`**<font style="color:rgba(0, 0, 0, 0.82);"> </font>****<font style="color:rgba(0, 0, 0, 0.82);">系统调用</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

`<font style="color:rgba(0, 0, 0, 0.82);">FileRegion</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">是 Netty 提供的一个接口，代表一个文件区域。通过使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">FileRegion</font>`<font style="color:rgba(0, 0, 0, 0.82);">，Netty 可以直接将文件数据从文件系统发送到网络通道。在底层，Netty 利用了操作系统提供的</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">sendfile</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">系统调用，这个调用允许直接在内核空间传输文件数据到网络接口，不需要用户空间的中转。</font>

```java
File file = new File("path/to/yourfile.txt");  
RandomAccessFile raf = new RandomAccessFile(file, "r");  
FileChannel fileChannel = raf.getChannel();  

// 在 Netty 中发送文件  
ChannelFuture future = channel.writeAndFlush(new DefaultFileRegion(fileChannel, 0, file.length()));  
future.addListener((ChannelFutureListener) future -> {  
    raf.close();  
});
```

2. `**<font style="color:rgba(0, 0, 0, 0.82);">CompositeByteBuf</font>**`<font style="color:rgba(0, 0, 0, 0.82);">：</font>

`<font style="color:rgba(0, 0, 0, 0.82);">CompositeByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">是 Netty 中的一个组合缓冲区，它允许将多个</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">实例组合成一个逻辑上的</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">而不需要实际的数据复制。这在需要处理包含多个部分（比如头部和主体）的数据时特别有用。</font>

```java
CompositeByteBuf compositeBuffer = Unpooled.compositeBuffer();  
ByteBuf header = Unpooled.copiedBuffer("Header".getBytes(StandardCharsets.UTF_8));  
ByteBuf body = Unpooled.copiedBuffer("Body".getBytes(StandardCharsets.UTF_8));  

compositeBuffer.addComponents(header, body);
```

3. **<font style="color:rgba(0, 0, 0, 0.82);">缓冲区的切片（Slicing）和复制（Duplication）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">提供了切片和复制功能，这些功能允许创建共享同一内存区域的多个</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">实例，从而避免不必要的数据拷贝。</font>

```java
ByteBuf buf = Unpooled.copiedBuffer("Netty Zero-Copy Example".getBytes(StandardCharsets.UTF_8));  

ByteBuf sliced = buf.slice(0, 5); // 创建一个切片，引用了相同的内存区域  
ByteBuf duplicated = buf.duplicate(); // 创建一个副本，共享相同的内存区域
```

4. **<font style="color:rgba(0, 0, 0, 0.82);">包装现有的缓冲区（Wrapped Buffer）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

<font style="color:rgba(0, 0, 0, 0.82);">可以通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">Unpooled.wrappedBuffer</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法将现有的数组或</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuffer</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">包装成</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">，而不需要进行数据的复制。</font>

```java
byte[] array = new byte[1024];  
ByteBuf wrappedBuffer = Unpooled.wrappedBuffer(array);
```

5. **<font style="color:rgba(0, 0, 0, 0.82);">直接内存（Direct Memory）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

<font style="color:rgba(0, 0, 0, 0.82);">Netty 支持直接内存（Direct Memory），即使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">DirectByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);">。直接内存缓冲区是分配在堆外内存的，可以直接被操作系统和硬件设备访问，避免了 JVM 堆和操作系统之间的数据复制。</font>

```java
ByteBuf directBuffer = Unpooled.directBuffer(1024);
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">零拷贝技术通过减少数据在内存中的复制过程，显著提高了数据传输的性能。Netty 通过一系列的机制，包括 </font>`<font style="color:rgba(0, 0, 0, 0.82);">sendfile</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 系统调用、</font>`<font style="color:rgba(0, 0, 0, 0.82);">CompositeByteBuf</font>`<font style="color:rgba(0, 0, 0, 0.82);">、缓冲区的切片和复制、包装现有的缓冲区、以及直接内存，来实现零拷贝。这些技术的结合，使得 Netty 成为一个高效且强大的网络编程框架，能够在高并发和大数据传输场景下表现出色。</font>



> 更新: 2024-08-05 20:38:46  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ekoctkf5nztzo0ky>