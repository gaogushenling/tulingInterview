# Redis的大Key问题如何解决

<font style="color:rgba(0, 0, 0, 0.82);">Redis的大Key问题是指单个Key所对应的数据量过大，一般单个key超过10kb就被认为是大Key，这会导致以下问题：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">网络延迟增大</font>**<font style="color:rgba(0, 0, 0, 0.82);">：传输大数据需要更多的时间。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">阻塞Redis性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：大Key的操作会阻塞Redis单线程的性能。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">内存不足和导致OOM</font>**<font style="color:rgba(0, 0, 0, 0.82);">：大Key可能会占用过多内存，影响其它部分的缓存使用。</font>

<font style="color:rgba(0, 0, 0, 0.82);">为了解决这些问题，可以采取以下解决方案：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">解决方案</font>
#### <font style="color:rgba(0, 0, 0, 0.82);">1. </font>**<font style="color:rgba(0, 0, 0, 0.82);">分拆大Key</font>**
<font style="color:rgba(0, 0, 0, 0.82);">big list： list1、list2、...listN</font>

<font style="color:rgb(74, 74, 74);">big hash：可以将数据分段存储，比如一个大的key，假设存了1百万的用户数据，可以拆分成200个key，每个key下面存放5000个用户数据</font>

<font style="color:rgb(74, 74, 74);"></font>

<font style="color:rgba(0, 0, 0, 0.82);">2. </font>**<font style="color:rgba(0, 0, 0, 0.82);">压缩数据</font>**

<font style="color:rgba(0, 0, 0, 0.82);">在存储之前对较大的数据进行压缩，从而减少存储占用空间。</font>

```java
import java.util.zip.GZIPOutputStream;  
import java.io.ByteArrayOutputStream;  
import java.io.IOException;  
import java.nio.charset.StandardCharsets;  

// Example of compressing data before storing  
public class DataCompressor {  
    public byte[] compress(String data) throws IOException {  
        ByteArrayOutputStream bos = new ByteArrayOutputStream(data.length());  
        GZIPOutputStream gzip = new GZIPOutputStream(bos);  
        gzip.write(data.getBytes(StandardCharsets.UTF_8));  
        gzip.close();  
        return bos.toByteArray();  
    }  

    public void storeCompressedKey(String key, String data) {  
        try {  
            byte[] compressedData = compress(data);  
            RedisClient.set(key.getBytes(StandardCharsets.UTF_8), compressedData);  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
}
```

#### <font style="color:rgba(0, 0, 0, 0.82);">3.</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>**<font style="color:rgba(0, 0, 0, 0.82);">惰性删除</font>**
<font style="color:rgba(0, 0, 0, 0.82);">当更新或删除大Key时使用惰性删除(</font>**<font style="color:rgb(74, 74, 74);">lazyfree-lazy-expire yes</font>**<font style="color:rgb(74, 74, 74);"></font><font style="color:rgba(0, 0, 0, 0.82);">)来避免阻塞整个Redis。</font>

<font style="color:rgba(0, 0, 0, 0.82);">4. </font>**<font style="color:rgba(0, 0, 0, 0.82);">使用SCAN替代KEYS</font>**

<font style="color:rgba(0, 0, 0, 0.82);">在处理集合时，使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">SCAN</font>`<font style="color:rgba(0, 0, 0, 0.82);">命令遍历大Key而不是</font>`<font style="color:rgba(0, 0, 0, 0.82);">KEYS</font>`<font style="color:rgba(0, 0, 0, 0.82);">，避免一次性加载所有数据。</font>

```java
import redis.clients.jedis.Jedis;  
import redis.clients.jedis.ScanParams;  
import redis.clients.jedis.ScanResult;  

import java.util.List;  

// Example: Iterating through large set with SCAN  
public class RedisScanner {  
    private Jedis jedis;  

    public RedisScanner(Jedis jedis) {  
        this.jedis = jedis;  
    }  

    public void scanLargeKey(String largeSetKey) {  
        String cursor = ScanParams.SCAN_POINTER_START;  
        ScanParams scanParams = new ScanParams().count(100); // 分批次取100个  
        do {  
            ScanResult<String> scanResult = jedis.sscan(largeSetKey, cursor, scanParams);  
            List<String> results = scanResult.getResult();  
            processResults(results);  
            cursor = scanResult.getCursor();  
        } while (!cursor.equals(ScanParams.SCAN_POINTER_START));  
    }  

    private void processResults(List<String> results) {  
        // 处理结果集  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">结论</font>
<font style="color:rgba(0, 0, 0, 0.82);">分片、压缩、异步删除和合理的遍历方式可以有效解决Redis大Key问题。在实际应用中，根据具体的使用场景和系统架构选择和组合这些方案。此外，要定期监控Redis节点的内存和数据使用情况，优化大Key的管理。注意，解决大Key问题往往需要架构上的设计调整。</font>



> 更新: 2024-08-07 21:31:02  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/fg51mlsueyfivzrh>