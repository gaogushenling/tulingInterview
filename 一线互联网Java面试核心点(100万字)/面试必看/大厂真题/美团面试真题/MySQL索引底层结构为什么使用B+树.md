# MySQL 索引底层结构为什么使用 B+树

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">MySQL索引底层结构使用B+树的主要原因有以下几点：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">能够支持快速的查找：B+树是一种平衡多路查找树，树的高度相对较低，能够快速定位到目标数据。在具有大量数据的情况下，B+树的查找效率更高。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">有序性：B+树的特点是节点上的键值是有序排列的，这使得在范围查询、排序和分组等操作中效率更高。对于MySQL的查询操作经常涉及范围查询，B+树能够有效支持这些操作。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">支持高效的插入和删除操作：B+树的平衡特性使得插入和删除操作相对简单，不需要进行大量的数据迁移。B+树的叶子节点之间使用双向链表连接，可以快速定位到要插入或删除的位置。</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">适应硬盘存储：MySQL通常需要将数据库存储在硬盘上，而不是内存中。B+树的节点大小被控制在硬盘页面大小的范围内，使得每次读取的数据量最大化，减少了IO操作。</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">支持数据的有序存储和范围查找：B+树的特点是有序性，这对于范围查询非常有利。通过B+树的索引，可以快速定位起始位置和结束位置，进行范围查询等操作。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">综上所述，B+树结构在存储大量数据、支持范围查询、有序性和高效的插入删除操作等方面具有优势，因此被广泛应用于MySQL索引的底层结构。</font>



> 更新: 2023-08-24 20:29:53  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/zbuoqncc5w52hv3h>