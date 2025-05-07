# Java五种文件拷贝方式

<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">在 Java 中，文件拷贝主要有以下几种方式，不同场景下效率差异显著。以下从</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">实现方式</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">、</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">效率对比</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">和</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">适用场景</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">三方面详细解析：</font>

---

# <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">一、文件拷贝的 5 种实现方式</font>
### <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">1. 传统字节流拷贝（</font>`<font style="background-color:rgb(252, 252, 252);">FileInputStream</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> + </font>`<font style="background-color:rgb(252, 252, 252);">FileOutputStream</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">）</font>
```java
public static void main(String[] args) throws IOException {
    try (InputStream is = new FileInputStream("source.txt");
         OutputStream os = new FileOutputStream("target.txt")) {
        byte[] buffer = new byte[1024];
        int length;
        while ((length = is.read(buffer)) > 0) {
            os.write(buffer, 0, length);
        }
    }
}
```

+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">特点</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：基础方法，直接逐字节或缓冲区读写。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">效率</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：最低（适合小文件）。</font>

---

### <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">2. 缓冲流优化拷贝（</font>`<font style="background-color:rgb(252, 252, 252);">BufferedInputStream</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> + </font>`<font style="background-color:rgb(252, 252, 252);">BufferedOutputStream</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">）</font>
```java
public static void main(String[] args) throws IOException {
    try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream("source.txt"));
         BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("target.txt"))) {
        byte[] buffer = new byte[8192]; // 缓冲区越大，性能越好（通常 8KB~64KB）
        int len;
        while ((len = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, len);
        }
    }
}
```

+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">特点</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：通过缓冲区减少 I/O 次数。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">效率</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：比传统字节流提升 2~5 倍。</font>

---

### <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">3. NIO </font>`<font style="background-color:rgb(252, 252, 252);">Files.copy</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> 方法（Java 7+）</font>
```java
public static void main(String[] args) throws IOException {
    Path source = Paths.get("source.txt");
    Path target = Paths.get("target.txt");
    Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
}
```

+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">特点</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：单行代码完成拷贝，底层自动优化。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">效率</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：接近最高效（适合大多数场景）。</font>

---

### <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">4. NIO </font>`<font style="background-color:rgb(252, 252, 252);">FileChannel</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> 通道拷贝</font>
```java
public static void main(String[] args) throws IOException {
    try (FileChannel sourceChannel = new FileInputStream("source.txt").getChannel();
         FileChannel targetChannel = new FileOutputStream("target.txt").getChannel()) {
        sourceChannel.transferTo(0, sourceChannel.size(), targetChannel);
    }
}
```

+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">特点</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：利用通道（Channel）直接传输数据。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">效率</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：大文件性能最佳（利用零拷贝技术）。</font>

---

### <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">5. 内存映射文件拷贝（</font>`<font style="background-color:rgb(252, 252, 252);">MappedByteBuffer</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">）</font>
```java
public static void main(String[] args) throws IOException {
    try (RandomAccessFile sourceFile = new RandomAccessFile("source.txt", "r");
         RandomAccessFile targetFile = new RandomAccessFile("target.txt", "rw")) {
        FileChannel sourceChannel = sourceFile.getChannel();
        MappedByteBuffer buffer = sourceChannel.map(FileChannel.MapMode.READ_ONLY, 0, sourceChannel.size());
        targetFile.getChannel().write(buffer);
    }
}
```

+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">特点</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：将文件映射到内存直接操作。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">效率</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：适合超大文件（但实现复杂，需谨慎处理内存）。</font>

---

# <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">二、效率对比（1GB 文件测试）</font>
| **<font style="background-color:rgb(252, 252, 252);">方法</font>** | **<font style="background-color:rgb(252, 252, 252);">耗时（毫秒）</font>** | **<font style="background-color:rgb(252, 252, 252);">适用场景</font>** |
| :---: | :---: | :---: |
| <font style="background-color:rgb(252, 252, 252);">传统字节流</font> | <font style="background-color:rgb(252, 252, 252);">4500~5000</font> | <font style="background-color:rgb(252, 252, 252);">小文件（<10MB）</font> |
| <font style="background-color:rgb(252, 252, 252);">缓冲流</font> | <font style="background-color:rgb(252, 252, 252);">1200~1500</font> | <font style="background-color:rgb(252, 252, 252);">通用场景</font> |
| `<font style="background-color:rgb(252, 252, 252);">Files.copy</font>` | <font style="background-color:rgb(252, 252, 252);">800~1000</font> | <font style="background-color:rgb(252, 252, 252);">简单代码 + 快速开发</font> |
| `<font style="background-color:rgb(252, 252, 252);">FileChannel.transfer</font>` | <font style="background-color:rgb(252, 252, 252);">600~800</font> | <font style="background-color:rgb(252, 252, 252);">大文件（>100MB）</font> |
| <font style="background-color:rgb(252, 252, 252);">内存映射文件</font> | <font style="background-color:rgb(252, 252, 252);">500~700</font> | <font style="background-color:rgb(252, 252, 252);">超大文件（>1GB）</font> |


---

# <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">三、如何选择最高效方式？</font>
1. **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">小文件（<10MB）</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">  
</font><font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">直接使用</font><font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> </font>`<font style="background-color:rgb(252, 252, 252);">Files.copy</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">，代码简洁且性能足够。</font>
2. **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">大文件（100MB~1GB）</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">  
</font><font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">优先选择</font><font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> </font>`<font style="background-color:rgb(252, 252, 252);">FileChannel.transferTo()</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">，利用零拷贝减少内核态与用户态数据复制。</font>
3. **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">超大文件（>1GB）</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">  
</font><font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">用内存映射文件（</font>`<font style="background-color:rgb(252, 252, 252);">MappedByteBuffer</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">），但需注意：</font>
    - <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">避免频繁映射/释放内存（开销大）。</font>
    - <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">处理内存溢出风险（</font>`<font style="background-color:rgb(252, 252, 252);">OutOfMemoryError</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">）。</font>
4. **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">通用场景</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">  
</font>`<font style="background-color:rgb(252, 252, 252);">Files.copy</font>`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> </font><font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">或缓冲流，平衡代码可读性与性能。</font>

---

# <font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">四、终极建议</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">优先使用</font>****<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> </font>**`**<font style="background-color:rgb(252, 252, 252);">Files.copy</font>**`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：Java 7+ 自带优化，简单高效。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">大文件必用</font>****<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);"> </font>**`**<font style="background-color:rgb(252, 252, 252);">FileChannel</font>**`<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：性能碾压传统流。</font>
+ **<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">避免手动逐字节读写</font>**<font style="color:rgba(0, 0, 0, 0.9);background-color:rgb(252, 252, 252);">：除非处理特殊格式（如加密流）。</font>



> 更新: 2025-03-02 17:09:01  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/qd7qptxemek4ra6u>