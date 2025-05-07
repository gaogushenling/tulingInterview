# Innodb加索引的时候会锁表吗

<font style="color:rgba(0, 0, 0, 0.82);">在 InnoDB 中，操作索引时是否锁表取决于具体的操作类型。有些情况可能会导致表锁，而有些情况则不会。以下是一些需要考虑的方面：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 添加索引时</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">在线添加索引（Online DDL）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">从 MySQL 5.6 开始，InnoDB 支持在线添加索引。这意味着在添加索引期间，表可以同时进行读取和写入操作，从而不会锁住整张表。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在线 DDL 的特性允许通过特定配置，让表在加索引的同时保持可用状态（如</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">LOCK=NONE</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">选项）。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">锁级别</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">默认情况下，即使是在线添加索引，也可能会对表的某些部分进行短暂的锁定，但不会长期锁表。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">需要注意的是，在不支持在线 DDL 的旧版本中，添加索引通常是涉及锁表的操作，尤其是在大量数据情况下。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. 删除索引</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">删除索引操作通常不需要长时间锁表，但在删除过程中可能进行短暂的锁定以保证元数据更新的一致性。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">3. 修改索引</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">对于某些索引修改，可能会引发锁表现象，尤其是在不支持在线 DDL 的情况下或在某些特定场景下，需要评估具体MySQL版本和参数配置。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">4. 操作期间的锁定影响</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">使用在线 DDL 功能时，可能依旧有性能上的影响，比如对 I/O 的消耗增加。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">因此进行大规模索引修改或添加操作建议在低峰期或通过分片、复制环境测试后实施。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">小结</font>
<font style="color:rgba(0, 0, 0, 0.82);">在现代 MySQL 版本中，InnoDB 通过支持在线 DDL 特性，在大多数索引相关操作中不需要锁表，这是为了降低对生产环境的影响。在进行索引操作时，务必检查使用的 MySQL 版本支持的功能，并合理配置相关参数以实现并发数据访问和操作的最小化冲突。</font>



> 更新: 2024-08-08 14:53:42  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ritpzfmgxd64fz4z>