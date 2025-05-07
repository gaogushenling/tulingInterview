# MySQL进阶系列：Join算法详解

![1734939656801-5952ce0c-a337-4404-bdd6-dd6dfcbc6d0d.png](./img/df4iORs61Mu2pAkn/1734939656801-5952ce0c-a337-4404-bdd6-dd6dfcbc6d0d-595206.png)

## <font style="color:rgb(31, 41, 55);">前言</font>
<font style="color:rgb(31, 41, 55);">在数据库开发中，</font>`<font style="color:rgb(31, 41, 55);">JOIN</font>`<font style="color:rgb(31, 41, 55);"> </font><font style="color:rgb(31, 41, 55);">是多表查询的核心操作，其性能直接影响整个系统的效率。</font>

<font style="color:rgb(31, 41, 55);">MySQL 提供了多种 </font>`<font style="color:rgb(31, 41, 55);">JOIN</font>`<font style="color:rgb(31, 41, 55);"> 算法来处理表之间的连接操作，包括</font>

+ <font style="color:rgb(31, 41, 55);">嵌套循环连接（Nested Loop Join）</font>
+ <font style="color:rgb(31, 41, 55);">索引嵌套循环连接（Index Nested Loop Join）</font>
+ <font style="color:rgb(31, 41, 55);">块嵌套循环连接（Block Nested Loop Join）</font>
+ <font style="color:rgb(31, 41, 55);">哈希连接（Hash Join）</font>
+ <font style="color:rgb(31, 41, 55);">排序合并连接（Sort-Merge Join）</font>

<font style="color:rgb(31, 41, 55);">这些算法各有优劣，适用于不同的场景。本文将详细解析这些 </font>`<font style="color:rgb(31, 41, 55);">JOIN</font>`<font style="color:rgb(31, 41, 55);"> 算法的底层实现原理，并结合实际案例和优化建议，帮助开发者更好地理解和优化多表查询。</font>

## <font style="color:rgb(31, 41, 55);">1. 简单嵌套循环连接（Simple Nested-Loop Join, SNLJ）</font>
### <font style="color:rgb(31, 41, 55);">原理</font>
<font style="color:rgb(31, 41, 55);">驱动表中的每一条记录，会依次与被驱动表中的每一条记录进行比较，寻找符合条件的匹配记录。这是最基本的实现方式，几乎不依赖任何辅助优化。</font>

### <font style="color:rgb(31, 41, 55);">特点</font>
<font style="color:rgb(31, 41, 55);">SNLJ 的实现简单，直接，同时也非常低效。因此，通常仅在小表连接场景下使用。</font>

### <font style="color:rgb(31, 41, 55);">优缺点</font>
+ **<font style="color:rgb(31, 41, 55);">优点：</font>**<font style="color:rgb(31, 41, 55);">实现极其简单，可广泛应用于快速验证。</font>
+ **<font style="color:rgb(31, 41, 55);">缺点：</font>**<font style="color:rgb(31, 41, 55);">性能差，对大数据量的查询非常低效，尤其是驱动表和被驱动表数据均较大时。</font>

### <font style="color:rgb(31, 41, 55);">执行流程图</font>
<font style="color:rgb(31, 41, 55);">简单嵌套循环连接（SNLJ）是最基础的连接算法，其执行过程如下：</font>

+ <font style="color:rgb(31, 41, 55);">从驱动表中取出一条记录。</font>
+ <font style="color:rgb(31, 41, 55);">遍历被驱动表中的所有记录，找到匹配的记录。</font>
+ <font style="color:rgb(31, 41, 55);">将匹配结果加入结果集。</font>

![1734937321227-f780ca95-3d5a-447c-9507-ae9063ec8391.png](./img/df4iORs61Mu2pAkn/1734937321227-f780ca95-3d5a-447c-9507-ae9063ec8391-243764.png)

## <font style="color:rgb(31, 41, 55);">2. 索引嵌套循环连接（Index Nested-Loop Join, INLJ）</font>
### <font style="color:rgb(31, 41, 55);">原理</font>
<font style="color:rgb(31, 41, 55);">INLJ 是嵌套循环连接的优化版本，通过被驱动表上的索引，可以避免对其进行全表扫描，而是直接通过索引定位到匹配记录。</font>

### <font style="color:rgb(31, 41, 55);">特点</font>
<font style="color:rgb(31, 41, 55);">利用了被驱动表索引提升查询效率，非常适合**小表连接大表**或者**大表间的少量匹配场景**。</font>

### <font style="color:rgb(31, 41, 55);">优缺点</font>
+ **<font style="color:rgb(31, 41, 55);">优点：</font>**<font style="color:rgb(31, 41, 55);">速度快，大幅减少了被驱动表的扫描次数。</font>
+ **<font style="color:rgb(31, 41, 55);">缺点：</font>**<font style="color:rgb(31, 41, 55);">依赖被驱动表索引；如果索引丢失，性能直接退化为简单嵌套循环。</font>

### <font style="color:rgb(31, 41, 55);">执行流程图</font>
<font style="color:rgb(31, 41, 55);">索引嵌套循环连接（INLJ）通过利用被驱动表的索引，显著减少匹配次数。其执行过程如下：</font>

+ <font style="color:rgb(31, 41, 55);">从驱动表中取出一条记录。</font>
+ <font style="color:rgb(31, 41, 55);">使用该记录的连接键，通过索引快速定位被驱动表中的匹配记录。</font>
+ <font style="color:rgb(31, 41, 55);">将匹配结果加入结果集。</font>

![1734936881477-738b0ad6-8297-4b3b-84ea-ca6f6ae1efd7.png](./img/df4iORs61Mu2pAkn/1734936881477-738b0ad6-8297-4b3b-84ea-ca6f6ae1efd7-017133.png)

## <font style="color:rgb(31, 41, 55);">3. 块嵌套循环连接（Block Nested-Loop Join, BNLJ）</font>
### <font style="color:rgb(31, 41, 55);">原理</font>
<font style="color:rgb(31, 41, 55);">在 BNLJ 中，驱动表的一部分数据会首先被加载到内存缓冲区 (</font>`<font style="color:rgb(31, 41, 55);">JOIN BUFFER</font>`<font style="color:rgb(31, 41, 55);">) 中，然后再与被驱动表进行比较匹配操作。</font>

### <font style="color:rgb(31, 41, 55);">特点</font>
<font style="color:rgb(31, 41, 55);">通过批量加载缓冲区减少被驱动表的扫描次数，适合于**大表之间无索引连接**的场景。</font>

### <font style="color:rgb(31, 41, 55);">优缺点</font>
+ **<font style="color:rgb(31, 41, 55);">优点：</font>**<font style="color:rgb(31, 41, 55);">缓冲机制可以显著降低被驱动表扫描次数。</font>
+ **<font style="color:rgb(31, 41, 55);">缺点：</font>**<font style="color:rgb(31, 41, 55);">对内存消耗较大，缓冲区不足会导致效率下降。</font>

### <font style="color:rgb(31, 41, 55);">执行流程图</font>
<font style="color:rgb(31, 41, 55);">块嵌套循环连接（BNLJ）通过引入</font><font style="color:rgb(31, 41, 55);"> </font>`<font style="color:rgb(31, 41, 55);">JOIN BUFFER</font>`<font style="color:rgb(31, 41, 55);"> </font><font style="color:rgb(31, 41, 55);">缓冲区，减少被驱动表的 I/O 次数。其执行过程如下：</font>

+ <font style="color:rgb(31, 41, 55);">将驱动表的一部分数据加载到</font><font style="color:rgb(31, 41, 55);"> </font>`<font style="color:rgb(31, 41, 55);">JOIN BUFFER</font>`<font style="color:rgb(31, 41, 55);"> </font><font style="color:rgb(31, 41, 55);">中。</font>
+ <font style="color:rgb(31, 41, 55);">遍历被驱动表的所有记录，与</font><font style="color:rgb(31, 41, 55);"> </font>`<font style="color:rgb(31, 41, 55);">JOIN BUFFER</font>`<font style="color:rgb(31, 41, 55);"> </font><font style="color:rgb(31, 41, 55);">中的数据进行匹配。</font>
+ <font style="color:rgb(31, 41, 55);">重复上述过程，直到驱动表的数据全部处理完毕。</font>

![1734936931411-9ea68a1b-7c62-4c12-813a-3021171718db.png](./img/df4iORs61Mu2pAkn/1734936931411-9ea68a1b-7c62-4c12-813a-3021171718db-866206.png)

## <font style="color:rgb(31, 41, 55);">4. 哈希连接（Hash Join）</font>
### <font style="color:rgb(31, 41, 55);">原理</font>
<font style="color:rgb(31, 41, 55);">哈希连接是一种高效的连接算法，适用于**等值连接**场景。其核心思想是：</font>

1. **<font style="color:rgb(31, 41, 55);">构建阶段：</font>**<font style="color:rgb(31, 41, 55);">以较小的表为基准，基于连接键构建哈希表（作为构建表）。</font>
2. **<font style="color:rgb(31, 41, 55);">探测阶段：</font>**<font style="color:rgb(31, 41, 55);">扫描较大的表（探测表），根据哈希值快速定位构建表中的匹配项。</font>

### <font style="color:rgb(31, 41, 55);">特点</font>
<font style="color:rgb(31, 41, 55);">哈希连接适用于需要连接的大表之间数据量大、缺少索引、但连接条件是**等值条件**的情况。</font>

### <font style="color:rgb(31, 41, 55);">优缺点</font>
+ **<font style="color:rgb(31, 41, 55);">优点：</font>**<font style="color:rgb(31, 41, 55);">在等值连接上效率极高，尤其适合无索引的大数据集。</font>
+ **<font style="color:rgb(31, 41, 55);">缺点：</font>**<font style="color:rgb(31, 41, 55);">无法处理范围条件、不等式条件；哈希表构建阶段对内存的依赖较大。</font>

### <font style="color:rgb(31, 41, 55);">执行流程图</font>
<font style="color:rgb(31, 41, 55);">哈希连接（Hash Join）是 MySQL 8.0.20 引入的一种高效连接算法，适用于大数据集的等值连接。其执行过程如下：</font>

+ **<font style="color:rgb(31, 41, 55);">构建阶段</font>**<font style="color:rgb(31, 41, 55);">：将较小的表加载到内存中，并基于连接键构建哈希表。</font>
+ **<font style="color:rgb(31, 41, 55);">探测阶段</font>**<font style="color:rgb(31, 41, 55);">：扫描较大的表，根据连接键查找哈希表中的匹配记录。</font>

![1734936902592-891c31c0-0466-47d9-84e3-a0a77d098378.png](./img/df4iORs61Mu2pAkn/1734936902592-891c31c0-0466-47d9-84e3-a0a77d098378-911236.png)

## <font style="color:rgb(31, 41, 55);">5. 排序合并连接（Sort-Merge Join）</font>
### <font style="color:rgb(31, 41, 55);">原理</font>
<font style="color:rgb(31, 41, 55);">排序合并连接通过**先对表的连接键进行排序**，然后使用类似归并排序的方式合并两个表。步骤如下：</font>

1. <font style="color:rgb(31, 41, 55);">为表 A 和表 B 基于连接键进行排序。</font>
2. <font style="color:rgb(31, 41, 55);">分别从两个排序后的表读取数据，对比连接键并合并匹配的记录。</font>
3. <font style="color:rgb(31, 41, 55);">对于不满足条件的记录，通过移动较小的一方指针解决。</font>

### <font style="color:rgb(31, 41, 55);">特点</font>
<font style="color:rgb(31, 41, 55);">非常适合于需要排序的大表或者需要进行范围条件连接的时候。</font>

### <font style="color:rgb(31, 41, 55);">优缺点</font>
+ **<font style="color:rgb(31, 41, 55);">优点：</font>**<font style="color:rgb(31, 41, 55);">适用于范围条件和等值条件，特别适合超大数据集。</font>
+ **<font style="color:rgb(31, 41, 55);">缺点：</font>**<font style="color:rgb(31, 41, 55);">排序阶段可能会导致较高的 I/O 开销。</font>

### <font style="color:rgb(31, 41, 55);">执行流程图</font>
<font style="color:rgb(31, 41, 55);">排序合并连接（Sort-Merge Join）是一种高效的连接算法，特别适用于没有索引的大数据集和需要范围连接的场景。其执行过程如下：</font>

+ **<font style="color:rgb(31, 41, 55);">排序阶段</font>**<font style="color:rgb(31, 41, 55);">：对两个表的连接键进行排序。</font>
+ **<font style="color:rgb(31, 41, 55);">合并阶段</font>**<font style="color:rgb(31, 41, 55);">：使用类似“归并排序”的方式，依次扫描两个排序后的表。</font>

![1734936912988-db408d2a-4b61-43f8-aec4-20732aaf3d51.png](./img/df4iORs61Mu2pAkn/1734936912988-db408d2a-4b61-43f8-aec4-20732aaf3d51-471156.png)

## <font style="color:rgb(31, 41, 55);">6. JOIN 算法对比</font>
<font style="color:rgb(31, 41, 55);">以下为五种 JOIN 算法的总结性对比，为开发者选择合适算法提供参考：</font>

| <font style="color:rgb(31, 41, 55);">算法</font> | <font style="color:rgb(31, 41, 55);">适用场景</font> | <font style="color:rgb(31, 41, 55);">优点</font> | <font style="color:rgb(31, 41, 55);">缺点</font> |
| --- | --- | --- | --- |
| <font style="color:rgb(31, 41, 55);">SNLJ</font> | <font style="color:rgb(31, 41, 55);">小表连接小表</font> | <font style="color:rgb(31, 41, 55);">实现简单</font> | <font style="color:rgb(31, 41, 55);">性能差，只适合小表</font> |
| <font style="color:rgb(31, 41, 55);">INLJ</font> | <font style="color:rgb(31, 41, 55);">被驱动表有索引</font> | <font style="color:rgb(31, 41, 55);">显著提升性能</font> | <font style="color:rgb(31, 41, 55);">缺少索引时性能退化</font> |
| <font style="color:rgb(31, 41, 55);">BNLJ</font> | <font style="color:rgb(31, 41, 55);">无索引的大表连接</font> | <font style="color:rgb(31, 41, 55);">减少被驱动表扫描</font> | <font style="color:rgb(31, 41, 55);">对内存依赖较高</font> |
| <font style="color:rgb(31, 41, 55);">Hash Join</font> | <font style="color:rgb(31, 41, 55);">等值连接</font> | <font style="color:rgb(31, 41, 55);">等值连接效率高</font> | <font style="color:rgb(31, 41, 55);">无法处理范围查询</font> |
| <font style="color:rgb(31, 41, 55);">Sort-Merge Join</font> | <font style="color:rgb(31, 41, 55);">范围或无索引场景</font> | <font style="color:rgb(31, 41, 55);">支持范围条件</font> | <font style="color:rgb(31, 41, 55);">排序的 I/O 开销大</font> |


## <font style="color:rgb(31, 41, 55);">7. 总结</font>
<font style="color:rgb(31, 41, 55);">通过深入理解 JOIN 的实现原理和适用场景，可以根据需求选择合适的算法：</font>

+ **<font style="color:rgb(31, 41, 55);">SNLJ：</font>**<font style="color:rgb(31, 41, 55);">适合小表或快速验证。</font>
+ **<font style="color:rgb(31, 41, 55);">INLJ：</font>**<font style="color:rgb(31, 41, 55);">优选小表到大表且有索引的场景。</font>
+ **<font style="color:rgb(31, 41, 55);">BNLJ：</font>**<font style="color:rgb(31, 41, 55);">适用于无索引的场景。</font>
+ **<font style="color:rgb(31, 41, 55);">Hash Join：</font>**<font style="color:rgb(31, 41, 55);">等值连接的高效利器。</font>
+ **<font style="color:rgb(31, 41, 55);">Sort-Merge Join：</font>**<font style="color:rgb(31, 41, 55);">更适合范围查询和排序场景。</font>



> 更新: 2024-12-23 15:41:40  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/iq2e1mgrxm0499x4>