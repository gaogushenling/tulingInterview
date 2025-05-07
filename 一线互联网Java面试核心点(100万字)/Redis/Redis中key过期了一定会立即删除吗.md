# Redis中key过期了一定会立即删除吗

<font style="color:rgba(0, 0, 0, 0.82);">在 Redis 中，键的过期和实际删除并不是实时的，Redis 采用了一种惰性删除（lazy deletion）和定期删除（periodic expiration）相结合的机制来处理过期键。以下是这两种机制的具体运作方式：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 惰性删除（Lazy Deletion）</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：当客户端访问一个键时，Redis 会检查这个键是否已过期。如果键过期了，那么它会在被访问时被惰性删除。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">特点</font>**<font style="color:rgba(0, 0, 0, 0.82);">：这种机制意味着即使一个键过期了，只要它没有被访问，Redis 是不会去主动删除它的。因此，惰性删除并不保证过期键会立即消失。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. 定期删除（Active Expiration/Periodic Expiration）</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">机制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Redis 会周期性地随机抽取一部分设置了过期时间的键进行检查，并删除已过期的键。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">频率</font>**<font style="color:rgba(0, 0, 0, 0.82);">：定期删除由 Redis 的后台任务执行，大约每隔 100 毫秒进行一次扫描。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">配置</font>**<font style="color:rgba(0, 0, 0, 0.82);">：可以通过配置</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">hz</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">参数来调整 Redis 的内部任务执行频率，但这也会影响整个 Redis 进程的资源消耗。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">过期删除的影响和配置</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">内存管理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：过期键的删除直接影响内存的使用情况，虽然定期删除与惰性删除可以确保大部分过期键最终被删除，但由于它们不是严格实时的，所以某些键可能会在过期后继续占用内存直到被访问或被后台任务删除。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能影响</font>**<font style="color:rgba(0, 0, 0, 0.82);">：惰性删除对性能几乎没有影响，而定期删除的开销可配置，一般对性能影响较小。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
<font style="color:rgba(0, 0, 0, 0.82);">过期键的删除并不是实时保证的。如果一个键已经过期，但在极端情况下它可能会在短时间内继续占用内存，直到它被访问或垃圾收集机制生效。对于一些关键应用，可能需要考虑通过逻辑上管理这些过期对象，从而确保数据的时效性和内存的高效利用。</font>



> 更新: 2024-08-07 20:28:14  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/uxfs5b661xuythb5>