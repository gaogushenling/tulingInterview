# MySQL中like模糊查询如何优化

<font style="color:rgba(0, 0, 0, 0.82);">在MySQL中，</font>`<font style="color:rgba(0, 0, 0, 0.82);">LIKE</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 模糊查询可能会导致性能问题，特别是当使用通配符 </font>`<font style="color:rgba(0, 0, 0, 0.82);">%</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 开头时，因为这通常会导致全表扫描。以下方法可以帮助优化 </font>`<font style="color:rgba(0, 0, 0, 0.82);">LIKE</font>`<font style="color:rgba(0, 0, 0, 0.82);"> 模糊查询：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. 合理使用索引</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">前缀匹配</font>**<font style="color:rgba(0, 0, 0, 0.82);">：使用</font>`<font style="color:rgba(0, 0, 0, 0.82);">LIKE 'prefix%'</font>`<font style="color:rgba(0, 0, 0, 0.82);">的形式，这种情况MySQL能够利用索引，比如：</font>

```sql
SELECT * FROM users WHERE username LIKE 'John%';
```

<font style="color:rgba(0, 0, 0, 0.82);">如果</font>`<font style="color:rgba(0, 0, 0, 0.82);">username</font>`<font style="color:rgba(0, 0, 0, 0.82);">字段有索引，前缀匹配会用到索引。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. 使用反向索引</font>
<font style="color:rgba(0, 0, 0, 0.82);">对于需要匹配后缀的情况（即</font>`<font style="color:rgba(0, 0, 0, 0.82);">LIKE '%suffix'</font>`<font style="color:rgba(0, 0, 0, 0.82);">），可以创建一个辅助列存储反转字符串，并基于此列进行前缀匹配。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">创建反向字符串</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>

```sql
ALTER TABLE users ADD reversed_username VARCHAR(255);  
UPDATE users SET reversed_username = REVERSE(username);  
CREATE INDEX idx_reversed_username ON users(reversed_username);
```

### <font style="color:rgba(0, 0, 0, 0.82);">3. 限制扫描范围</font>
<font style="color:rgba(0, 0, 0, 0.82);">在LIKE查询中，如果可以通过其他条件进一步缩小搜索范围，请尽量加入这些条件。</font>

```sql
SELECT * FROM users WHERE created_at >= '2023-01-01' AND username LIKE 'John%';
```

### <font style="color:rgba(0, 0, 0, 0.82);">4. 使用缓存</font>
<font style="color:rgba(0, 0, 0, 0.82);">如果相同的查询需要频繁执行，可以考虑在应用层实施缓存策略来减少数据库负载。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">5. 使用专业工具</font>
<font style="color:rgba(0, 0, 0, 0.82);">对于非常大的数据集或者需要复杂文本处理和搜索功能，可以使用外部全文搜索引擎如Elasticsearch、Solr或者Sphinx来代替MySQL的LIKE操作。</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些方法优化LIKE查询，可以显著提升数据库的查询性能。应根据具体场景选择合适的优化策略。使用EXPLAIN分析查询执行计划，可以帮助确认优化效果。</font>

<font style="color:rgba(0, 0, 0, 0.82);"></font>

[bilibili](https://player.bilibili.com/player.html?bvid=BV1kFpue5Ehj&p=5&page=5&autoplay=0)



> 更新: 2024-09-13 19:57:57  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/wiz8zcex83ch9zt6>