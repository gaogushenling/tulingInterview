# MySQL的深度分页如何优化

<font style="color:rgba(0, 0, 0, 0.82);">在MySQL中，当涉及到深度分页时（offset较大），性能可能会显著下降。这是因为MySQL需要扫描和跳过大量行才能到达所需的偏移量。这会导致大量的磁盘I/O和内存消耗，进而影响查询性能。以下是一些常用的优化深度分页的方法：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 基于主键或唯一索引的优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">利用主键或唯一索引可以显著提高深度分页的效率，因为它们能够指向特定的行。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">记录上次查询的最大ID</font>**<font style="color:rgba(0, 0, 0, 0.82);">（或其他唯一标识符），并以此为起点进行下一次查询。这种方法需要有连续的、唯一的列（如自增ID）以用于分页。</font>

```sql
SELECT * FROM table WHERE id > last_seen_id ORDER BY id ASC LIMIT page_size;
```

### <font style="color:rgba(0, 0, 0, 0.82);">2. 使用子查询优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">通过子查询的方式，仅选择需要的记录ID，然后再通过这些ID提取完整记录。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">子查询结合JOIN</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```sql
SELECT t.* FROM table t  
JOIN (  
  SELECT id FROM table ORDER BY id LIMIT offset, page_size  
) sub ON t.id = sub.id;
```

### <font style="color:rgba(0, 0, 0, 0.82);">3. 覆盖索引</font>
<font style="color:rgba(0, 0, 0, 0.82);">确保分页操作使用的查询能够被覆盖索引满足，从而减少了回表（访问数据页）的次数。这样可以提高查询速度。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">4. 缓存机制</font>
<font style="color:rgba(0, 0, 0, 0.82);">在应用层面实现结果集缓存，避免重复查询。例如，可以将分页结果缓存到Redis或Memcached中。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">5. 视业务情况优化</font>
<font style="color:rgba(0, 0, 0, 0.82);">根据特定业务需求，可能可以优化显示的逻辑。例如，一些应用可以通过取得一定范围的结果并在客户端分页来减少数据库的负担。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">6. 限制最大页数</font>
<font style="color:rgba(0, 0, 0, 0.82);">限制应用程序中最大可翻页数，避免用户访问深度分页。提示数据太旧或已过时，要求用户进行新的查询。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过结合上述方法，可以有效地减少深度分页带来的性能影响。具体选择何种策略需要考虑数据特性和业务需求。使用EXPLAIN命令分析每个查询计划，以确保优化真正有效。</font>

<font style="color:rgba(0, 0, 0, 0.82);"></font>

[bilibili](https://player.bilibili.com/player.html?bvid=BV1kFpue5Ehj&p=9&page=9&autoplay=0)



> 更新: 2024-09-13 19:59:26  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/oqryrca5gyvy0y8q>