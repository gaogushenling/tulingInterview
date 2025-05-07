# MySQL中NULL 和 NOT NULL 一定不走索引

<font style="color:rgba(6, 8, 31, 0.88);">在 MySQL 中，字段是否允许为 NULL 以及字段的定义方式会直接影响查询条件 </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 和 </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 是否使用索引。此外，字段中 NULL 值的分布占比也会显著影响查询性能和优化器的决策。</font>

<font style="color:rgba(6, 8, 31, 0.88);">本文通过实验分析了字段定义为 </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 和允许为 </font>`<font style="color:rgba(6, 8, 31, 0.88);">NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> 时的查询行为，并结合 NULL 值分布占比的测试结果，总结了优化器的决策逻辑和相关结论。</font>

---

### <font style="color:rgba(6, 8, 31, 0.88);">一、实验背景</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">1. 测试目标</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">验证字段定义为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">时，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">和</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询是否走索引。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">验证字段允许为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">时，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">和</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询是否走索引。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">探讨字段中 NULL 值分布占比对查询性能和索引使用的影响。</font>

#### <font style="color:rgba(6, 8, 31, 0.88);">2. 测试表结构</font>
<font style="color:rgba(6, 8, 31, 0.88);">创建测试表</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">staffs</font>`<font style="color:rgba(6, 8, 31, 0.88);">，初始字段定义为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">，并插入测试数据。</font>

```plsql
CREATE TABLE staffs (  
  id INT PRIMARY KEY AUTO_INCREMENT,  
  name VARCHAR(24) NOT NULL DEFAULT '' COMMENT '姓名',  
  age INT NOT NULL DEFAULT 0 COMMENT '年龄',  
  pos VARCHAR(20) NOT NULL DEFAULT '' COMMENT '职位',  
  add_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '入职时间'  
) CHARSET utf8 COMMENT '员工记录表'; 

-- 为 name、age、pos 添加联合索引  
ALTER TABLE staffs ADD INDEX idx_staffs_nameAgePos (name, age, pos);

-- 插入测试数据  
INSERT INTO staffs (name, age, pos) VALUES ('z3', 22, 'manager');  
INSERT INTO staffs (name, age, pos) VALUES ('July', 23, 'dev');  
INSERT INTO staffs (name, age, pos) VALUES ('2000', 23, 'dev');  
```

<font style="color:rgba(6, 8, 31, 0.88);">表数据如下：</font>

```plsql
SELECT * FROM staffs;  
+----+------+-----+---------+---------------------+
| id | name | age | pos     | add_time            |
+----+------+-----+---------+---------------------+
|  1 | z3   |  22 | manager | 2024-12-17 16:47:45 |
|  2 | July |  23 | dev     | 2024-12-17 16:47:45 |
|  3 | 2000 |  23 | dev     | 2024-12-17 16:47:45 |
+----+------+-----+---------+---------------------+
```

---

### <font style="color:rgba(6, 8, 31, 0.88);">二、测试过程与结果</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">1. 字段定义为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">时的查询行为</font>
##### <font style="color:rgba(6, 8, 31, 0.88);">查询 1：</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`
```plsql
EXPLAIN SELECT * FROM staffs WHERE name IS NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">查询条件</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">被认为是</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">Impossible WHERE</font>**<font style="color:rgba(6, 8, 31, 0.88);">，即不可能成立的条件。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">执行计划显示：</font>

```plsql
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra            |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
|  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | Impossible WHERE |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
```

+ **<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：字段</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">定义为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">，因此不可能有 NULL 值，MySQL 优化器直接跳过扫描，返回空集。</font>

##### <font style="color:rgba(6, 8, 31, 0.88);">查询 2：</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`
```plsql
EXPLAIN SELECT * FROM staffs WHERE name IS NOT NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">查询条件</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">没有走索引，而是进行了全表扫描。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">执行计划显示：</font>

```plsql
+----+-------------+--------+------------+------+-----------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys         | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-----------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | idx_staffs_nameAgePos | NULL | NULL    | NULL |    3 |    66.67 | Using where |
+----+-------------+--------+------------+------+-----------------------+------+---------+------+------+----------+-------------+
```

+ **<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：字段</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">定义为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">，所有记录都符合</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">条件，优化器认为全表扫描成本更低，因此未使用索引。</font>

##### <font style="color:rgba(6, 8, 31, 0.88);">查询 3：覆盖索引优化</font>
```plsql
EXPLAIN SELECT name FROM staffs WHERE name IS NOT NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">查询条件</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">使用了覆盖索引。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">执行计划显示：</font>

```plsql
----+-------------+--------+------------+-------+-----------------------+-----------------------+---------+------+------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys         | key                   | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+--------+------------+-------+-----------------------+-----------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | staffs | NULL       | index | idx_staffs_nameAgePos | idx_staffs_nameAgePos | 140     | NULL |    3 |    66.67 | Using where; Using index |
+----+-------------+--------+------------+-------+-----------------------+-----------------------+---------+------+------+----------+--------------------------+
```

+ **<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：查询只涉及索引字段</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);">，无需回表操作，因此优化器选择使用覆盖索引。</font>

---

#### <font style="color:rgba(6, 8, 31, 0.88);">2. 修改字段为允许 NULL 后的查询行为</font>
<font style="color:rgba(6, 8, 31, 0.88);">将字段</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">修改为允许 NULL，并更新部分数据。</font>

```plsql
ALTER TABLE staffs MODIFY name VARCHAR(24) DEFAULT NULL COMMENT '姓名';  

-- 更新部分数据  
UPDATE staffs SET name = NULL WHERE id = 2;  

-- 查看表数据  
SELECT * FROM staffs;  
----+------+-----+---------+---------------------+
| id | name | age | pos     | add_time            |
+----+------+-----+---------+---------------------+
|  1 | z3   |  22 | manager | 2024-12-17 16:47:45 |
|  2 | NULL |  23 | dev     | 2024-12-17 16:47:45 |
|  3 | 2000 |  23 | dev     | 2024-12-17 16:47:45 |
+----+------+-----+---------+---------------------+
```

##### <font style="color:rgba(6, 8, 31, 0.88);">查询 1：</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`
```plsql
EXPLAIN SELECT * FROM staffs WHERE name IS NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">查询条件</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">使用了索引。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">执行计划显示：</font>

```plsql
+----+-------------+--------+------------+------+-----------------------+-----------------------+---------+-------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys         | key                   | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+-----------------------+-----------------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | ref  | idx_staffs_nameAgePos | idx_staffs_nameAgePos | 75      | const |    1 |   100.00 | Using index condition |
+----+-------------+--------+------------+------+-----------------------+-----------------------+---------+-------+------+----------+-----------------------+
```

+ **<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：字段</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">允许为 NULL，且存在 NULL 值，优化器选择使用索引扫描匹配 NULL 值的记录。</font>

##### <font style="color:rgba(6, 8, 31, 0.88);">查询 2：</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`
```plsql
EXPLAIN SELECT * FROM staffs WHERE name IS NOT NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">查询条件</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">使用了索引。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">执行计划显示：</font>

```plsql
+----+-------------+--------+------------+-------+-----------------------+-----------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys         | key                   | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+-----------------------+-----------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | range | idx_staffs_nameAgePos | idx_staffs_nameAgePos | 75      | NULL |    2 |   100.00 | Using index condition |
+----+-------------+--------+------------+-------+-----------------------+-----------------------+---------+------+------+----------+-----------------------+
```

+ **<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：字段</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">允许为 NULL，优化器通过索引扫描匹配非 NULL 值的记录。</font>

---

### <font style="color:rgba(6, 8, 31, 0.88);">三、实验结论</font>
1. **<font style="color:rgba(6, 8, 31, 0.88);">字段定义为</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>**`**<font style="color:rgba(6, 8, 31, 0.88);"> </font>****<font style="color:rgba(6, 8, 31, 0.88);">时</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">查询</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">被认为是</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>**<font style="color:rgba(6, 8, 31, 0.88);">Impossible WHERE</font>**<font style="color:rgba(6, 8, 31, 0.88);">，不会走索引。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">查询</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">通常不走索引，而是全表扫描。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">如果查询只涉及索引字段（覆盖索引场景），</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询可以走索引。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">字段允许为 NULL 时</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">查询</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">和</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">都可以走索引。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">NULL 值分布占比会显著影响查询性能和索引使用。</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">NULL 值分布占比的影响</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">当 NULL 值占比较小时，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询更倾向于使用索引。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">当非 NULL 值占比较小时，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询更倾向于使用索引。</font>

---

### <font style="color:rgba(6, 8, 31, 0.88);">四、优化建议</font>
1. **<font style="color:rgba(6, 8, 31, 0.88);">合理设计字段属性</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">如果字段不会存储 NULL 值，建议定义为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">，以减少 NULL 值处理的开销。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">如果字段需要存储 NULL 值，确保查询条件能够充分利用索引。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">定期更新统计信息</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">ANALYZE TABLE</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">更新表的统计信息，确保优化器的成本估算更准确。</font>

<font style="color:rgba(6, 8, 31, 0.88);">通过结合 NULL 值分布占比的分析，可以更好地理解 MySQL 优化器的行为，并设计合理的表结构和索引策略以提升查询性能。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">五、 NULL 值分布占比对查询的影响</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">1. 测试环境准备</font>
<font style="color:rgba(6, 8, 31, 0.88);">创建测试表</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">test</font>`<font style="color:rgba(6, 8, 31, 0.88);">，并为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">和</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">code</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段分别添加二级索引。</font>

```plsql
CREATE TABLE test (  
  id INT AUTO_INCREMENT PRIMARY KEY,  
  name VARCHAR(50) DEFAULT NULL,  
  code INT DEFAULT NULL  
);  

CREATE INDEX idx_name ON test(name);  
CREATE INDEX idx_code ON test(code);
```

<font style="color:rgba(6, 8, 31, 0.88);">插入 150 万条数据，分别测试以下两种情况：</font>

1. `**<font style="color:rgba(6, 8, 31, 0.88);">name</font>**`**<font style="color:rgba(6, 8, 31, 0.88);"> </font>****<font style="color:rgba(6, 8, 31, 0.88);">字段绝大多数为非 NULL，少量为 NULL</font>**<font style="color:rgba(6, 8, 31, 0.88);">。</font>
2. `**<font style="color:rgba(6, 8, 31, 0.88);">code</font>**`**<font style="color:rgba(6, 8, 31, 0.88);"> </font>****<font style="color:rgba(6, 8, 31, 0.88);">字段绝大多数为 NULL，少量为非 NULL</font>**<font style="color:rgba(6, 8, 31, 0.88);">。</font>

```plsql
DELIMITER //  

-- 插入数据，name 字段绝大多数为非 NULL  
CREATE PROCEDURE insert_data_name()  
BEGIN  
    DECLARE i INT DEFAULT 1;  
    WHILE i <= 1500000 DO  
        IF i % 100 = 0 THEN  
            INSERT INTO test (name, code) VALUES (NULL, NULL);  
        ELSE  
            INSERT INTO test (name, code) VALUES (CONCAT('name', i), NULL);  
        END IF;  
        SET i = i + 1;  
    END WHILE;  
END //  

-- 插入数据，code 字段绝大多数为 NULL  
CREATE PROCEDURE insert_data_code()  
BEGIN  
    DECLARE i INT DEFAULT 1;  
    WHILE i <= 1500000 DO  
        IF i % 100 = 0 THEN  
            INSERT INTO test (name, code) VALUES (NULL, i);  
        ELSE  
            INSERT INTO test (name, code) VALUES (NULL, NULL);  
        END IF;  
        SET i = i + 1;  
    END WHILE;  
END //  

-- 执行存储过程插入数据  
CALL insert_data_name();  
CALL insert_data_code();
```

---

#### <font style="color:rgba(6, 8, 31, 0.88);">2. 测试结果</font>
##### <font style="color:rgba(6, 8, 31, 0.88);">2.1</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段（绝大多数为非 NULL）</font>
1. **<font style="color:rgba(6, 8, 31, 0.88);">查询</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">name IS NULL</font>**`

```plsql
EXPLAIN SELECT * FROM test WHERE name IS NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：走索引</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">idx_name</font>`<font style="color:rgba(6, 8, 31, 0.88);">。  
</font>**<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段中 NULL 值占比小，扫描行数较少，优化器认为使用索引成本更低。</font>

2. **<font style="color:rgba(6, 8, 31, 0.88);">查询</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">name IS NOT NULL</font>**`

```plsql
EXPLAIN SELECT * FROM test WHERE name IS NOT NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：不走索引，进行全表扫描。  
</font>**<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段中非 NULL 值占比大，扫描行数接近全表，优化器认为全表扫描成本更低。</font>

---

##### <font style="color:rgba(6, 8, 31, 0.88);">2.2</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">code</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段（绝大多数为 NULL）</font>
1. **<font style="color:rgba(6, 8, 31, 0.88);">查询</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">code IS NOT NULL</font>**`

```plsql
EXPLAIN SELECT * FROM test WHERE code IS NOT NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：走索引</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">idx_code</font>`<font style="color:rgba(6, 8, 31, 0.88);">。  
</font>**<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>`<font style="color:rgba(6, 8, 31, 0.88);">code</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段中非 NULL 值占比小，扫描行数较少，优化器认为使用索引成本更低。</font>

2. **<font style="color:rgba(6, 8, 31, 0.88);">查询</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">code IS NULL</font>**`

```plsql
EXPLAIN SELECT * FROM test WHERE code IS NULL;
```

**<font style="color:rgba(6, 8, 31, 0.88);">结果</font>**<font style="color:rgba(6, 8, 31, 0.88);">：走索引</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">idx_code</font>`<font style="color:rgba(6, 8, 31, 0.88);">。  
</font>**<font style="color:rgba(6, 8, 31, 0.88);">原因</font>**<font style="color:rgba(6, 8, 31, 0.88);">：优化器统计的</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">rows</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">值不准确，错误地认为使用索引扫描行数较少。</font>

---

#### <font style="color:rgba(6, 8, 31, 0.88);">3、原理分析</font>
##### <font style="color:rgba(6, 8, 31, 0.88);">1. NULL 在 B+ 树中的存储方式</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">在 MySQL 的二级索引中，NULL 被认为小于任何值，因此键值为 NULL 的行记录被存储在 B+ 树的最左侧。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">查找</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">时，优化器会从 B+ 树的最左侧开始扫描，直到遇到非 NULL 值为止。</font>

##### <font style="color:rgba(6, 8, 31, 0.88);">2. 优化器的成本计算</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">优化器会根据统计信息估算扫描行数和成本，决定是否使用索引。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">如果查询条件筛选出的行数占总行数的比例较大（如</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询），优化器可能选择全表扫描。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">如果统计信息不准确（如</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">rows</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">值估算错误），优化器可能错误地选择使用索引。</font>

##### <font style="color:rgba(6, 8, 31, 0.88);">3. 回表操作的影响</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">二级索引的叶子节点存储的是索引键值和主键值，查询</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">SELECT *</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">时需要通过主键值回表获取完整数据。</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">如果扫描的二级索引行数较多，回表次数也会增加，可能导致性能下降。</font>

---

#### <font style="color:rgba(6, 8, 31, 0.88);">4、优化建议</font>
##### <font style="color:rgba(6, 8, 31, 0.88);">1. 避免优化器误判</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">如果优化器误判导致查询性能下降，可以通过强制使用索引来验证：</font>

```plsql
SELECT * FROM test FORCE INDEX (idx_name) WHERE name IS NULL;
```

##### <font style="color:rgba(6, 8, 31, 0.88);">2. 减少回表操作</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">如果查询只需要索引字段，可以避免</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">SELECT *</font>`<font style="color:rgba(6, 8, 31, 0.88);">，改为只查询需要的字段：</font>

```plsql
SELECT name FROM test WHERE name IS NULL;
```

##### <font style="color:rgba(6, 8, 31, 0.88);">3. 使用覆盖索引</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">如果查询的字段都包含在索引中，可以通过覆盖索引优化查询性能：</font>

```plsql
ALTER TABLE test ADD INDEX idx_name_code (name, code);  
SELECT name, code FROM test WHERE name IS NULL;
```

##### <font style="color:rgba(6, 8, 31, 0.88);">4. 更新统计信息</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">定期使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">ANALYZE TABLE</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">更新表的统计信息，确保优化器的成本估算更准确：</font>

```plsql
ANALYZE TABLE test;
```

---

#### <font style="color:rgba(6, 8, 31, 0.88);">5、总结</font>
<font style="color:rgba(6, 8, 31, 0.88);">通过测试可以得出以下结论：</font>

1. **<font style="color:rgba(6, 8, 31, 0.88);">字段中 NULL 值占比对索引使用有重要影响</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">NULL 值占比小，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询走索引，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询可能全表扫描。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">NULL 值占比大，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询走索引，</font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">查询可能全表扫描。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">优化器的成本计算可能出现误判</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">统计信息不准确时，优化器可能错误地选择使用索引。</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">回表操作可能导致性能下降</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">二级索引扫描行数过多时，回表成本可能高于全表扫描。</font>

<font style="color:rgba(6, 8, 31, 0.88);">在实际开发中，可以通过强制索引、覆盖索引等方式优化查询性能，同时定期更新统计信息，避免优化器误判。</font>



> 更新: 2024-12-17 22:19:44  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/sm47fo5o5k271hzx>