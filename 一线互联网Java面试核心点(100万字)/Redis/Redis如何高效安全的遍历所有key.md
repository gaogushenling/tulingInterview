# Redis如何高效安全的遍历所有key

<font style="color:rgba(0, 0, 0, 0.82);">在 Redis 中高效且安全地遍历所有键时，通常使用 </font>`<font style="color:rgba(0, 0, 0, 0.82);">SCAN</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 命令，这是因为 </font>`<font style="color:rgba(0, 0, 0, 0.82);">SCAN</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 可以避免一次性加载所有 Key 到内存中（这会耗费大量内存和阻塞服务器），并且它支持增量式迭代。以下是使用 Java 和 Jedis 库来遍历 Redis 中所有键的示例：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">SCAN 命令的特点</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">非阻塞</font>**<font style="color:rgba(0, 0, 0, 0.82);">：不像</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">KEYS</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">命令，该命令不会阻塞 Redis。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">增量式</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">SCAN</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">命令提供了游标，当你使用完一个游标后，可以继续用返回的游标继续扫描。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">Java 示例代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">以下示例展示了如何使用 Jedis 客户端与 Redis 进行交互并使用 SCAN 命令遍历所有键：</font>

```java
import redis.clients.jedis.Jedis;  
import redis.clients.jedis.ScanParams;  
import redis.clients.jedis.ScanResult;  

import java.util.List;  

public class RedisKeyScanner {  

    public static void main(String[] args) {  
        // 建立 Redis 连接  
        Jedis jedis = new Jedis("localhost", 6379);  

        // 初始化 SCAN 游标  
        String cursor = ScanParams.SCAN_POINTER_START;  

        // 扫描参数  
        ScanParams scanParams = new ScanParams();  
        scanParams.match("*"); // 匹配所有模式，可以指定模式  
        scanParams.count(10); // 每次扫描返回的个数，这个值可调  

        do {  
            // 使用 SCAN 命令遍历键  
            ScanResult<String> scanResult = jedis.scan(cursor, scanParams);  
            List<String> keys = scanResult.getResult();  
            cursor = scanResult.getCursor();  

            // 处理每次返回的键  
            for (String key : keys) {  
                System.out.println("Found key: " + key);  
            }  

        } while (!cursor.equals(ScanParams.SCAN_POINTER_START)); // SCAN 命令从头到尾遍历  

        // 关闭 Redis 连接  
        jedis.close();  
    }  
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">代码说明</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">连接到 Redis</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">new Jedis("localhost", 6379)</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">连接到 Redis 服务。确保您的 Redis 服务正在运行并且允许外部连接。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">设置扫描参数</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">match("*")</font>`<font style="color:rgba(0, 0, 0, 0.82);">: 匹配所有键，但您可以修改为其他模式，例如</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">user:*</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">来只匹配用户键。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">count(10)</font>`<font style="color:rgba(0, 0, 0, 0.82);">: 每次返回的键数量。注意这仅是对 Redis 的建议，它并不是硬性规则。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">遍历所有键</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">jedis.scan(cursor, scanParams)</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">方法获取指定游标的键。</font>
    - `<font style="color:rgba(0, 0, 0, 0.82);">cursor</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">用于记录扫描结果的游标位置，它在每次 SCAN 操作后更新。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">处理键</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">遍历</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">scanResult.getResult()</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">获得的所有键，并进行处理（这里只是简单打印出来）。</font>
5. **<font style="color:rgba(0, 0, 0, 0.82);">关闭连接</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用完毕后关闭 Jedis 连接释放资源。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">安全和高效性</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">非阻塞</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">SCAN</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">命令会在可能的情况下游标继续扫描，避免像</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">KEYS</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">这样的阻塞操作。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">内存友好</font>**<font style="color:rgba(0, 0, 0, 0.82);">：只会加载一部分键到客户端内存中。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">避免大规模操作风险</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>`<font style="color:rgba(0, 0, 0, 0.82);">SCAN</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">允许服务器在轻度负载下的分批次地遍历数据集合。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这种方式，您可以在不影响 Redis 性能的情况下安全地遍历所有键，并有效地处理大型数据集。一旦数据规模变得相当大，推荐增加扫描频率参数以适应场景需要。</font>



> 更新: 2024-08-07 20:33:56  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/eszavn6v6cw9n77i>