# InnoDB存储引擎三大特性

### <font style="color:rgb(6, 6, 7);">Buffer Pool</font>
+ **<font style="color:rgb(6, 6, 7);">原理</font>**<font style="color:rgb(6, 6, 7);">：Buffer Pool是InnoDB存储引擎用于缓存数据页和索引页的内存区域。它提高了数据库的读写性能，因为数据页和索引页在内存中读写比在磁盘上快得多。当需要访问数据时，InnoDB会先在Buffer Pool中查找，如果找不到，才会从磁盘读取。</font>
+ **<font style="color:rgb(6, 6, 7);">工作方式</font>**<font style="color:rgb(6, 6, 7);">：Buffer Pool采用LRU（最近最少使用）算法来管理内存中的页。当Buffer Pool满时，最近最少使用的页会被淘汰，以便为新的页腾出空间。</font>
+ **<font style="color:rgb(6, 6, 7);">配置参数</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - `innodb_buffer_pool_size`<font style="color:rgb(6, 6, 7);">：设置Buffer Pool的大小，通常建议设置为物理内存的70%左右，以平衡数据库和其他系统的需求。</font>
    - `innodb_buffer_pool_instances`<font style="color:rgb(6, 6, 7);">：设置Buffer Pool的实例数量，用于减少锁竞争，提高并发性能。</font>
+ **<font style="color:rgb(6, 6, 7);">应用场景</font>**<font style="color:rgb(6, 6, 7);">：适用于需要频繁读写数据的场景，如事务处理、查询优化等。</font>

### <font style="color:rgb(6, 6, 7);">Adaptive Hash Index</font>
+ **<font style="color:rgb(6, 6, 7);">原理</font>**<font style="color:rgb(6, 6, 7);">：Adaptive Hash Index（AHI）是InnoDB存储引擎的一种内存结构，用于加速等值查询。InnoDB会自动监控索引的使用情况，如果发现某个索引频繁被访问，就会在内存中为该索引创建哈希索引，以减少查询时间。</font>
+ **<font style="color:rgb(6, 6, 7);">工作方式</font>**<font style="color:rgb(6, 6, 7);">：AHI是一个哈希表结构，键是索引键值，值是该键值对应的页面位置。需</font>二级索 频繁查询（固定时间内连续<font style="color:rgb(143, 143, 143);">多次</font>等值查询==号,>=等不行，orderby 无效）成为热点数据会建立hash index 带来速度的提升。可通过 %hash_index% 查询
+ **<font style="color:rgb(6, 6, 7);">配置参数</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - `innodb_adaptive_hash_index`<font style="color:rgb(6, 6, 7);">：控制是否启用AHI，默认启用。在高并发场景下，如果CPU资源紧张，可以考虑禁用。</font>
    - `innodb_adaptive_hash_index_partitions`<font style="color:rgb(6, 6, 7);">：设置AHI的分区数量，增加分区数量可以减少哈希冲突，但也会增加内存消耗。</font>
+ **<font style="color:rgb(6, 6, 7);">应用场景</font>**<font style="color:rgb(6, 6, 7);">：适用于频繁的等值查询，如主键查询或唯一索引查询。在高并发读取的场景下，AHI可以减少磁盘I/O，提高查询响应速度。</font>
+ **<font style="color:rgb(6, 6, 7);">限制</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">只能用于等值比较，如</font>`=`<font style="color:rgb(6, 6, 7);">、</font>`<=>`<font style="color:rgb(6, 6, 7);">、</font>`IN`<font style="color:rgb(6, 6, 7);">等。</font>
    - <font style="color:rgb(6, 6, 7);">无法用于排序。</font>
    - <font style="color:rgb(6, 6, 7);">存在哈希冲突的可能性，可能导致性能下降。</font>
    - <font style="color:rgb(6, 6, 7);">维护AHI需要额外的内存和CPU资源。</font>

### <font style="color:rgb(6, 6, 7);">Double Write</font>
+ **<font style="color:rgb(6, 6, 7);">原理</font>**<font style="color:rgb(6, 6, 7);">：Double Write是InnoDB存储引擎用于提高数据完整性和可靠性的机制。它通过在将数据页写入数据文件之前，先将它们写入一个称为Double Write Buffer的连续存储区域，确保数据的一致性。如果在写入过程中发生崩溃，InnoDB可以从Double Write Buffer中恢复数据。</font>
+ **<font style="color:rgb(6, 6, 7);">工作方式</font>**<font style="color:rgb(6, 6, 7);">：数据页首先被写入Double Write Buffer，然后通过两次写入操作（顺序写）将数据页写入数据文件的适当位置。这种方式可以减少I/O开销，因为数据是连续写入的。</font>
+ **<font style="color:rgb(6, 6, 7);">配置参数</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - `innodb_doublewrite`<font style="color:rgb(6, 6, 7);">：控制是否启用Double Write功能。默认启用，但在某些性能测试场景下可以禁用。</font>
    - `innodb_doublewrite_dir`<font style="color:rgb(6, 6, 7);">：设置Double Write文件的存储目录。</font>
    - `innodb_doublewrite_files`<font style="color:rgb(6, 6, 7);">：定义Double Write文件的数量。</font>
    - `innodb_doublewrite_pages`<font style="color:rgb(6, 6, 7);">：控制每个线程的最大Double Write页数。</font>
+ **<font style="color:rgb(6, 6, 7);">应用场景</font>**<font style="color:rgb(6, 6, 7);">：适用于需要高可靠性和数据一致性的场景，如生产环境中的数据库系统。</font>

<font style="color:rgb(6, 6, 7);">以上是关于InnoDB存储引擎的三大特性：Buffer Pool、Adaptive Hash Index和Double Write的详细解释。这些特性在不同的场景下各有优势，可以根据具体需求进行配置和优化。</font>

<font style="color:rgb(6, 6, 7);"></font>

**<font style="color:rgb(6, 6, 7);">图解：</font>**

<font style="color:rgb(6, 6, 7);">API设计图：</font>

![1743593433908-6b651c28-062d-4ab9-b651-ba5ca4c8ff75.png](./img/NUrD4xe9tY5pa7gS/1743593433908-6b651c28-062d-4ab9-b651-ba5ca4c8ff75-452460.png)

BufferPool设计

![1743593485682-1ddf9fcf-d435-4f01-8c77-948714877b54.png](./img/NUrD4xe9tY5pa7gS/1743593485682-1ddf9fcf-d435-4f01-8c77-948714877b54-885879.png)

淘汰策略

![1743593386366-95e6504e-7a06-48f6-8cd0-2b93f3ec796f.png](./img/NUrD4xe9tY5pa7gS/1743593386366-95e6504e-7a06-48f6-8cd0-2b93f3ec796f-243239.png)

doublewrite双写机制

![1743593355545-aebcf763-12c0-4935-8b76-e8bde8e8ddbc.png](./img/NUrD4xe9tY5pa7gS/1743593355545-aebcf763-12c0-4935-8b76-e8bde8e8ddbc-855191.png)

<font style="color:rgb(6, 6, 7);"></font>



> 更新: 2025-04-02 19:31:52  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rdw1hy38srbek0f9>