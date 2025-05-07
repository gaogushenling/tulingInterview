# MySQL死锁问题如何排查

<font style="color:rgba(0, 0, 0, 0.82);">MySQL数据库死锁问题比较常见，接下来通过一个死锁排查过程的例子给大家讲解。这个例子将模拟一个实际的死锁场景,然后一步步展示如何识别、分析和解决这个死锁问题。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">场景描述</font>
<font style="color:rgba(0, 0, 0, 0.82);">假设我们有一个在线商店系统,包含以下两个表:</font>

1. `<font style="color:rgba(0, 0, 0, 0.82);">products</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">(产品表)</font>
2. `<font style="color:rgba(0, 0, 0, 0.82);">orders</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">(订单表)</font>

<font style="color:rgba(0, 0, 0, 0.82);">两个并发事务试图更新这些表,导致了死锁。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">步骤1: 复现死锁</font>
<font style="color:rgba(0, 0, 0, 0.82);">首先,我们需要创建一个能够可靠复现死锁的场景。</font>

```java
-- 创建表  
CREATE TABLE products (  
    id INT PRIMARY KEY,  
    name VARCHAR(100),  
stock INT  
);  

CREATE TABLE orders (  
    id INT PRIMARY KEY,  
product_id INT,  
quantity INT  
);  

-- 插入初始数据  
INSERT INTO products VALUES (1, 'Product A', 100);  
INSERT INTO products VALUES (2, 'Product B', 200);  
INSERT INTO orders VALUES (1, 1, 5);  
INSERT INTO orders VALUES (2, 2, 10);
```

<font style="color:rgba(0, 0, 0, 0.82);">现在,我们模拟两个并发事务:</font>

<font style="color:rgba(0, 0, 0, 0.82);">事务1:</font>

```java
START TRANSACTION;  
UPDATE products SET stock = stock - 5 WHERE id = 1;  
-- 模拟延迟  
DO SLEEP(2);  
UPDATE orders SET quantity = quantity + 5 WHERE id = 1;  
COMMIT;
```

<font style="color:rgba(0, 0, 0, 0.82);">事务2:</font>

```java
START TRANSACTION;  
UPDATE orders SET quantity = quantity - 5 WHERE id = 1;  
-- 模拟延迟  
DO SLEEP(2);  
UPDATE products SET stock = stock + 5 WHERE id = 1;  
COMMIT;
```

### <font style="color:rgba(0, 0, 0, 0.82);">步骤2: 识别死锁</font>
<font style="color:rgba(0, 0, 0, 0.82);">当死锁发生时,MySQL会自动检测并回滚其中一个事务。我们可以通过以下方式来识别死锁:</font>

1. <font style="color:rgba(0, 0, 0, 0.82);">检查应用程序日志,寻找类似 "Deadlock found when trying to get lock" 的错误消息。</font>
2. <font style="color:rgba(0, 0, 0, 0.82);">使用MySQL命令查看最近的死锁信息:</font>

```java
SHOW ENGINE INNODB STATUS;
```

<font style="color:rgba(0, 0, 0, 0.82);">在输出中,找到 "LATEST DETECTED DEADLOCK" 部分。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">步骤3: 分析死锁</font>
<font style="color:rgba(0, 0, 0, 0.82);">从</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">SHOW ENGINE INNODB STATUS</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">的输出中,我们可以看到类似这样的信息:</font>

```java
------------------------  
LATEST DETECTED DEADLOCK  
------------------------  
*** (1) TRANSACTION:  
TRANSACTION 8-131, ACTIVE 6 sec starting index read  
mysql tables in use 1, locked 1  
LOCK WAIT 3 lock struct(s), heap size 1136, 2 row lock(s)  
                                                       MySQL thread id 8, OS thread handle 140286124944128, query id 57 localhost root updating  
UPDATE products SET stock = stock - 5 WHERE id = 1  

*** (2) TRANSACTION:  
TRANSACTION 8-132, ACTIVE 4 sec starting index read  
mysql tables in use 1, locked 1  
3 lock struct(s), heap size 1136, 2 row lock(s)  
                                             MySQL thread id 9, OS thread handle 140286124680960, query id 58 localhost root updating  
UPDATE orders SET quantity = quantity - 5 WHERE id = 1  

*** WE ROLL BACK TRANSACTION (1)
```

<font style="color:rgba(0, 0, 0, 0.82);">这个输出告诉我们:</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">事务1正在更新products表</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">事务2正在更新orders表</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">MySQL选择回滚事务1来解决死锁</font>

### <font style="color:rgba(0, 0, 0, 0.82);">步骤4: 解决死锁</font>
<font style="color:rgba(0, 0, 0, 0.82);">基于分析,我们可以采取以下措施来解决和预防死锁:</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">保持一致的访问顺序</font>**<font style="color:rgba(0, 0, 0, 0.82);">:  
</font><font style="color:rgba(0, 0, 0, 0.82);">修改应用程序代码,确保所有事务按照相同的顺序访问表 (例如,总是先访问products,再访问orders)。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">减少事务范围</font>**<font style="color:rgba(0, 0, 0, 0.82);">:  
</font><font style="color:rgba(0, 0, 0, 0.82);">尽可能缩小事务范围,减少持有锁的时间。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">使用乐观锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">:  
</font><font style="color:rgba(0, 0, 0, 0.82);">对于products表,可以使用版本号来实现乐观锁:</font>

```java
ALTER TABLE products ADD COLUMN version INT DEFAULT 0;  

-- 更新时检查版本号  
UPDATE products   
SET stock = stock - 5, version = version + 1   
WHERE id = 1 AND version = 0;
```

1. **<font style="color:rgba(0, 0, 0, 0.82);">添加适当的索引</font>**<font style="color:rgba(0, 0, 0, 0.82);">:  
</font><font style="color:rgba(0, 0, 0, 0.82);">确保</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">products.id</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">和</font><font style="color:rgba(0, 0, 0, 0.82);"> </font>`<font style="color:rgba(0, 0, 0, 0.82);">orders.id</font>`<font style="color:rgba(0, 0, 0, 0.82);"> </font><font style="color:rgba(0, 0, 0, 0.82);">有合适的索引。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">使用行级锁而不是表级锁</font>**<font style="color:rgba(0, 0, 0, 0.82);">:  
</font><font style="color:rgba(0, 0, 0, 0.82);">InnoDB默认使用行级锁,但确保不要使用会导致表级锁的操作(如</font>`<font style="color:rgba(0, 0, 0, 0.82);">LOCK TABLES</font>`<font style="color:rgba(0, 0, 0, 0.82);">)。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">步骤5: 监控和预防</font>
1. <font style="color:rgba(0, 0, 0, 0.82);">设置死锁监控:</font>

```java
SET GLOBAL innodb_print_all_deadlocks = 1;
```

<font style="color:rgba(0, 0, 0, 0.82);">这将把所有死锁信息记录到MySQL错误日志中。</font>

2. <font style="color:rgba(0, 0, 0, 0.82);">定期检查死锁情况:</font>

```java
SELECT * FROM information_schema.INNODB_TRX;
```

<font style="color:rgba(0, 0, 0, 0.82);">这可以查看当前正在执行的事务。</font>

3. <font style="color:rgba(0, 0, 0, 0.82);">使用性能模式(Performance Schema)来监控锁等待:</font>

```java
SELECT * FROM performance_schema.events_waits_current  
WHERE EVENT_NAME LIKE 'wait/synch/mutex/innodb%';
```

### <font style="color:rgba(0, 0, 0, 0.82);">结论</font>
<font style="color:rgba(0, 0, 0, 0.82);">通过以上步骤,我们可以有效地识别、分析和解决MySQL中的死锁问题。记住,预防死锁的关键在于合理设计数据库结构和事务逻辑,以及持续的监控和优化。在实际应用中,可能需要根据具体情况调整这些步骤和解决方案。</font>



> 更新: 2024-08-26 14:02:29  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/lgaw0d9podklhr3e>