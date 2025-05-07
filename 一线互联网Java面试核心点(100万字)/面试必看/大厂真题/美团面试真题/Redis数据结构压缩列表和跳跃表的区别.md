# Redis 数据结构压缩列表和跳跃表的区别

<font style="color:rgb(0,0,0);">压缩列表（ziplist）本质上就是一个字节数组，是 Redis 为了节约内存而设计的一种线性 </font>

<font style="color:rgb(0,0,0);">数据结构，可以包含多个元素，每个元素可以是一个字节数组或一个整数。 </font>

<font style="color:rgb(0,0,0);">跳跃表（skiplist）是一种有序数据结构，它通过在每个节点中维持多个指向其他节点的指 </font>

<font style="color:rgb(0,0,0);">针，从而达到快速访问节点的目的。跳跃表支持平均 O（logN）、最坏 O（N）复杂度的 </font>

<font style="color:rgb(0,0,0);">节点查找，还可以通过顺序性操作来批量处理节点。</font>



> 更新: 2023-08-24 20:50:51  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ggxehhvrhfzk829d>