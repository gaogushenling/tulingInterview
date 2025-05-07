# MySQL索引设计有哪些原则

<font style="color:rgba(0, 0, 0, 0.82);">在设计数据库索引时，遵循一些关键原则和最佳实践可以显著提升查询性能，同时减少系统资源的浪费。以下是设计索引时应考虑的重要原则：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 选择合适的列</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">频繁作为查询条件的列</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">对经常出现在</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">WHERE</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">子句中的列创建索引。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">参与排序的列</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">对</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">ORDER BY</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">GROUP BY</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">或者</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">DISTINCT</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">操作中出现的列建立索引。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">作为连接条件的列</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在多表连接的情况下，对作为连接条件的列创建索引。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. 优化多列索引</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">复合索引</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">对多个列组合查询的情况，考虑使用复合索引而非单个列索引，以减少表扫描次数。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">最左前缀匹配</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">确保复合索引的列顺序遵循最左前缀原则，即将使用最频繁的列放在最前面。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">3. 控制索引的数量和类型</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">避免过多索引</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">索引有利于查询，但每个新增索引会影响插入、更新、删除的性能。应在查询效率与维护成本之间取得平衡。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">适用索引类型</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">选择合适类型的索引，例如 B-tree 索引用于大多数情况，Full-text 索引用于全文搜索，Hash 索引用于等值查询（如内存表）。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">4. 考虑数据分布和选择性</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">高选择性的列</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">高基数、高选择性（具有许多不同值）的列索引往往能提高查询效率。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">均匀分布的数据</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">索引在数据分布均匀时效果最佳，避免对只有少数不同值的列（低基数）设置索引。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">5. 避免冗余和重复索引</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">合并索引</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">考虑合并类似用途的索引，减少冗余。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">定期清理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">定期审查和清理不再使用或效果不佳的索引。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">6. 使用覆盖索引</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">覆蓋索引</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">当索引包含查询所需的所有列时，称为覆盖索引，能有效减少读取数据页的次数。尽量使查询使用覆盖索引。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">7. 关注变更和维护</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">更新和插入的影响</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在设计索引时，必须注意对写操作（如插入、更新）的影响，确保系统的峰值写入性能。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">统计信息的刷新</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">确保及时更新统计信息，特别是在大量数据变动后，帮助查询优化器做出更优的决策。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">8. 考虑分区和分片</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">大数据集的优化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">对于非常大的数据集，考虑使用数据库的分区或分片功能，结合索引设计以提高性能。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">小结</font>
<font style="color:rgba(0, 0, 0, 0.82);">随着数据规模和访问模式的变化，索引设计并非一成不变，应根据应用的实际需求、查询特性和性能监控结果，持续调整和优化索引策略。通过合理设计算索引，可以在不显著增加存储空间和维护成本的前提下，最大化提升数据库的响应速度和整体性能。</font>



> 更新: 2024-08-08 15:09:24  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ilhr9xhhtrqcq3zd>