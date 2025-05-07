# MySQL的行级锁锁的到底是什么

<font style="color:rgba(0, 0, 0, 0.82);">MySQL 的 InnoDB 存储引擎实现的行级锁，实际上锁定的是基于索引的行记录。这意味着，行锁针对的是数据表中的索引记录，而非数据表本身的物理行。这一关键特性有以下几个具体的表现：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">行级锁的锁定对象</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">索引记录</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">InnoDB 行级锁会锁住某条索引记录。这意味着，如果要锁住一行数据，InnoDB 实际上需要锁住该行对应的索引项。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">即使在没有显式定义索引的情况下，InnoDB 也会使用隐式生成的主键索引来进行行锁。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">基于索引加锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果一个 SQL 语句使用了 WHERE 条件且条件字段是经过索引的，则 InnoDB 会锁定符合条件的索引项对应的行。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">如果表结构中没有索引，InnoDB 则会退而锁住整张表，类似于表锁。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">锁的运作和限制</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">使用索引进行精确加锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">精确的索引设计（包括主键和次级索引）对于高效行锁操作是至关重要的。这能保证锁住的只是相关的行，而不是误锁其他无关行。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">锁冲突的减少</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">行级锁允许多个事务在不同的行上进行并发操作，从而提高了数据库的并发性能。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">减少使用未加索引的列来避免无意间锁定过多行。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">隐式锁和显式锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">默认情况下，所有操作的锁都是由 InnoDB 自动管理的。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">开发者需要确保事务逻辑中使用索引字段进行条件过滤，以便行级锁能够正确应用。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">间隙锁和 Next-key 锁</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">间隙锁（Gap Lock）</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">除了锁住索引项本身，InnoDB 在某些隔离级别下（如 Repeatable Read）还会锁住索引项之间的间隙，以防止幻读。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">Next-Key Lock</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">是行锁与间隙锁结合形成的锁，用于精确地锁住相邻的索引记录和索引间隙，以防止插入操作引起的幻读。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">小结</font>
<font style="color:rgba(0, 0, 0, 0.82);">在 InnoDB 中，行级锁的具体对象是索引中的行记录。这实现了行粒度的高效并发控制，但要求数据库在使用上需合理设计索引以减少锁冲突和性能损失。通过理解和利用这一特性，开发者可以优化数据库性能和并发事务处理的效率。</font>



> 更新: 2024-08-08 14:52:28  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hq2igxh7xvr22w5r>