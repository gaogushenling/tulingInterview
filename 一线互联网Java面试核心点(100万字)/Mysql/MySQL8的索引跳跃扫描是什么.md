# MySQL 8的索引跳跃扫描是什么

<font style="color:rgba(0, 0, 0, 0.82);">MySQL中的索引跳跃扫描（Skip Scan）是一种优化策略，旨在提高在特定条件下对复合索引的利用效率。尽管MySQL并没有明确标记这项技术为"Skip Scan"功能，但通过其优化器的改进，特别是在MySQL 8.0.13及其后的版本中，MySQL可以在某些条件下实现类似Skip Scan的效果。让我们通过一个具体示例来说明其概念和潜在应用。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">示例背景</font>
<font style="color:rgba(0, 0, 0, 0.82);">假设有一个包含以下数据结构的表</font>`<font style="color:rgba(0, 0, 0, 0.82);">employees</font>`<font style="color:rgba(0, 0, 0, 0.82);">：</font>

| **<font style="color:rgba(0, 0, 0, 0.82);">ID</font>** | **<font style="color:rgba(0, 0, 0, 0.82);">Department</font>** | **<font style="color:rgba(0, 0, 0, 0.82);">Position</font>** |
| --- | --- | --- |
| <font style="color:rgba(0, 0, 0, 0.82);">1</font> | <font style="color:rgba(0, 0, 0, 0.82);">Sales</font> | <font style="color:rgba(0, 0, 0, 0.82);">Manager</font> |
| <font style="color:rgba(0, 0, 0, 0.82);">2</font> | <font style="color:rgba(0, 0, 0, 0.82);">Sales</font> | <font style="color:rgba(0, 0, 0, 0.82);">Executive</font> |
| <font style="color:rgba(0, 0, 0, 0.82);">3</font> | <font style="color:rgba(0, 0, 0, 0.82);">HR</font> | <font style="color:rgba(0, 0, 0, 0.82);">Manager</font> |
| <font style="color:rgba(0, 0, 0, 0.82);">4</font> | <font style="color:rgba(0, 0, 0, 0.82);">HR</font> | <font style="color:rgba(0, 0, 0, 0.82);">Executive</font> |
| <font style="color:rgba(0, 0, 0, 0.82);">5</font> | <font style="color:rgba(0, 0, 0, 0.82);">IT</font> | <font style="color:rgba(0, 0, 0, 0.82);">Manager</font> |
| <font style="color:rgba(0, 0, 0, 0.82);">6</font> | <font style="color:rgba(0, 0, 0, 0.82);">IT</font> | <font style="color:rgba(0, 0, 0, 0.82);">Executive</font> |


<font style="color:rgba(0, 0, 0, 0.82);">并且在表上有一个复合索引</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">(Department, Position)</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">查询场景</font>
<font style="color:rgba(0, 0, 0, 0.82);">假设我们希望查找所有</font>`<font style="color:rgba(0, 0, 0, 0.82);">Manager</font>`<font style="color:rgba(0, 0, 0, 0.82);">职位的员工：</font>

```sql
SELECT * FROM employees WHERE Position = 'Manager';
```

### <font style="color:rgba(0, 0, 0, 0.82);">传统索引处理</font>
<font style="color:rgba(0, 0, 0, 0.82);">如果正常按索引</font>`<font style="color:rgba(0, 0, 0, 0.82);">(Department, Position)</font>`<font style="color:rgba(0, 0, 0, 0.82);">的顺序使用，理想情况下需要首先给出</font>`<font style="color:rgba(0, 0, 0, 0.82);">Department</font>`<font style="color:rgba(0, 0, 0, 0.82);">条件，以充分利用索引优势，但本查询并没有提供对</font>`<font style="color:rgba(0, 0, 0, 0.82);">Department</font>`<font style="color:rgba(0, 0, 0, 0.82);">的条件。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">跳跃扫描的优化实现（概念性）</font>
<font style="color:rgba(0, 0, 0, 0.82);">通过类似Skip Scan的逻辑，MySQL优化器可能会：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">分区扫描</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">将索引按</font>`<font style="color:rgba(0, 0, 0, 0.82);">Department</font>`<font style="color:rgba(0, 0, 0, 0.82);">的唯一值进行分割。每个分区相当于不同的部门，比如</font>`<font style="color:rgba(0, 0, 0, 0.82);">Sales</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">HR</font>`<font style="color:rgba(0, 0, 0, 0.82);">、</font>`<font style="color:rgba(0, 0, 0, 0.82);">IT</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">应用条件</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">对每个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Department</font>`<font style="color:rgba(0, 0, 0, 0.82);">分区中的</font>`<font style="color:rgba(0, 0, 0, 0.82);">Position</font>`<font style="color:rgba(0, 0, 0, 0.82);">行进行扫描，即跳跃扫描中仅搜索</font>`<font style="color:rgba(0, 0, 0, 0.82);">Position = 'Manager'</font>`<font style="color:rgba(0, 0, 0, 0.82);">的员工。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">跳过无关分区</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">由于</font>`<font style="color:rgba(0, 0, 0, 0.82);">Position = 'Manager'</font>`<font style="color:rgba(0, 0, 0, 0.82);">限定，我们只在每个</font>`<font style="color:rgba(0, 0, 0, 0.82);">Department</font>`<font style="color:rgba(0, 0, 0, 0.82);">的分区中寻找匹配的</font>`<font style="color:rgba(0, 0, 0, 0.82);">Position</font>`<font style="color:rgba(0, 0, 0, 0.82);">，而不是对整个表进行扫描。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">实际效果</font>
<font style="color:rgba(0, 0, 0, 0.82);">虽然MySQL索引跳跃扫描概念上并不是一个独立标识的特性，但此类索引优化策略在新版MySQL中可能通过改进的索引条件推送和查询优化器来实现。它潜在地减少读取和滤除不必要数据的开销。</font>

<font style="color:rgba(0, 0, 0, 0.82);">这种策略在数据有较低基数（即</font>`<font style="color:rgba(0, 0, 0, 0.82);">Department</font>`<font style="color:rgba(0, 0, 0, 0.82);">的唯一值少）的场景中特别有效，因为跳跃的次数减少，提高了效率。可通过 </font>`<font style="color:rgba(0, 0, 0, 0.82);">EXPLAIN</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 检查特定查询的实际执行计划以确认优化的应用。</font>

<font style="color:rgba(0, 0, 0, 0.82);"></font>

[bilibili](https://player.bilibili.com/player.html?bvid=BV1kFpue5Ehj&p=11&page=11&autoplay=0)



> 更新: 2024-09-13 20:00:03  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vcsp5p0ziz5ubl8v>