# truncate、delete、drop的区别

`<font style="color:rgba(0, 0, 0, 0.82);">TRUNCATE</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">DELETE</font>`<font style="color:rgba(0, 0, 0, 0.82);">和</font>`<font style="color:rgba(0, 0, 0, 0.82);">DROP</font>`<font style="color:rgba(0, 0, 0, 0.82);">都是SQL中的数据操作语句，用于删除数据或表结构，但它们在功能、性能和使用场景上有显著的区别：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">TRUNCATE</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">用途</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">用于删除表中的所有数据，但保留表结构。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">比</font>`<font style="color:rgba(0, 0, 0, 0.82);">DELETE</font>`<font style="color:rgba(0, 0, 0, 0.82);">快，因为它通常不记录单独的行删除操作，也不触发行级触发器。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">事务处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">在大多数数据库系统中，</font>`<font style="color:rgba(0, 0, 0, 0.82);">TRUNCATE</font>`<font style="color:rgba(0, 0, 0, 0.82);">被视为一个DDL操作而非DML操作。因此，它可能不能被回滚（但这依赖于数据库的实现，某些系统可能支持事务性TRUNCATE）。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">自动递增</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">自动递增的值会被重置。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">触发器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">不会激活行级触发器。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">语法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```sql
TRUNCATE TABLE table_name;
```

### <font style="color:rgba(0, 0, 0, 0.82);">DELETE</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">用途</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">用于删除表中的指定行或所有数据，但保留表结构和约束。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">比</font>`<font style="color:rgba(0, 0, 0, 0.82);">TRUNCATE</font>`<font style="color:rgba(0, 0, 0, 0.82);">慢，特别是在处理大表时，因为它逐行记录删除操作，并能激活触发器。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">事务处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">是一个DML操作，支持事务处理，可以在事务中回滚。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">自动递增</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">自动递增的值不会被重置。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">触发器</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">激活行级触发器。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">语法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```sql
DELETE FROM table_name WHERE condition;
```

<font style="color:rgba(0, 0, 0, 0.82);">若要删除所有行但不重置自动递增：</font>

```sql
DELETE FROM table_name;
```

### <font style="color:rgba(0, 0, 0, 0.82);">DROP</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">用途</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">用于删除整个表、数据库或其他数据库对象，包括其结构和数据。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">性能</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">通常是立即操作，不可恢复。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">事务处理</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">是一个DDL操作，不可回滚（具体依赖数据库系统）。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">删除影响</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">删除表时会删除所有数据、索引、约束和权限信息。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">语法</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```sql
DROP TABLE table_name;
```

### <font style="color:rgba(0, 0, 0, 0.82);">总结</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">TRUNCATE</font>**<font style="color:rgba(0, 0, 0, 0.82);">：快速清空表，但保留结构。适用于需要清空但不删除表的场景。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">DELETE</font>**<font style="color:rgba(0, 0, 0, 0.82);">：逐行删除数据，可以选择性删除某些记录。适用于只想删除部分数据的场合，且支持事务。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">DROP</font>**<font style="color:rgba(0, 0, 0, 0.82);">：删除整个表定义及其所有数据。适用于不再需要表结构或其数据的情况。</font>

<font style="color:rgba(0, 0, 0, 0.82);">选择哪个语句取决于你的需求是否涉及数据完整性、安全性、性能优化或是事务处理</font>

<font style="color:rgba(0, 0, 0, 0.82);"></font>

[bilibili](https://player.bilibili.com/player.html?bvid=BV1kFpue5Ehj&p=10&page=10&autoplay=0)



> 更新: 2024-09-13 19:59:46  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/gcl128ydou9pzlp0>