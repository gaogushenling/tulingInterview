# 什么是索引下推？

<font style="color:rgba(0, 0, 0, 0.82);">索引下推（Index Condition Pushdown, ICP）是MySQL优化器的一项优化技术，旨在提高通过索引检索数据的效率，尤其是在InnoDB存储引擎中。这项技术在MySQL 5.6中引入，专门用于减少从表中读取不必要的行，提高查询性能。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">工作原理</font>
<font style="color:rgba(0, 0, 0, 0.82);">在没有索引下推的情况下，当查询使用复合索引时，MySQL可能需要访问主表来评估不能完全通过索引条件确定的行。例如，如果只有索引的部分条件在索引中能被使用，而其他条件需要读取实际行再进行筛选，传统方式可能会导致更多的全行读取。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过索引下推优化，MySQL可以在索引级别应用更多的查询过滤条件，从而减少读取主表的行数。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例</font>
<font style="color:rgba(0, 0, 0, 0.82);">假设有一个包含许多列的表</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">products</font>`<font style="color:rgba(0, 0, 0, 0.82);">，并为复合索引</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">(category_id, price)</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">创建以下查询：</font>

```sql
SELECT * FROM products WHERE category_id = 5 AND price < 100;
```

<font style="color:rgba(0, 0, 0, 0.82);">如果复合索引</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">(category_id, price)</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">存在：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">无索引下推</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">MySQL根据</font>`<font style="color:rgba(0, 0, 0, 0.82);">category_id = 5</font>`<font style="color:rgba(0, 0, 0, 0.82);">条件使用索引检索数据，然后访问主表的每一行以评估</font>`<font style="color:rgba(0, 0, 0, 0.82);">price < 100</font>`<font style="color:rgba(0, 0, 0, 0.82);">条件。这可能导致访问许多不满足价格条件的行。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">有索引下推</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">MySQL在索引层面直接评估</font>`<font style="color:rgba(0, 0, 0, 0.82);">price < 100</font>`<font style="color:rgba(0, 0, 0, 0.82);">的条件，只需将符合条件的行从表中提取。这减少了对表的I/O操作。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">适用场景</font>
<font style="color:rgba(0, 0, 0, 0.82);">索引下推非常适合于查询条件中涉及多个列的过滤，且这些列存在复合索引的情况下。它可以在索引扫描过程中应用额外的过滤条件来减少所需的行访问。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">优势</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">减少I/O操作</font>**<font style="color:rgba(0, 0, 0, 0.82);">：因为避免了对不必要的表记录的读取，所以可以大幅减少I/O操作。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">提高查询效率</font>**<font style="color:rgba(0, 0, 0, 0.82);">：直接在索引层面筛选数据，进一步减少了需要处理的数据量。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">更快的响应时间</font>**<font style="color:rgba(0, 0, 0, 0.82);">：整体上更有效的索引应用提高了查询响应速度。</font>

<font style="color:rgba(0, 0, 0, 0.82);">索引下推是MySQL智能优化器功能迅速发展的一个体现，熟练使用及理解该功能有助于优化复杂查询和提升性能。</font>



> 更新: 2024-08-08 16:33:45  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hmenpg2g8ncqkciq>