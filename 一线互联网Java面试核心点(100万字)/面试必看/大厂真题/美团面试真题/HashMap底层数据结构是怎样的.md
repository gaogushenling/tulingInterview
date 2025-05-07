# HashMap底层数据结构是怎样的

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">HashMap的底层数据结构主要由</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">数组</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">和</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">链表</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">（或</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">红黑树</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">）组成。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">HashMap内部维护了一个</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">Entry数组</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，用于存储键值对对象Entry。数组的每个元素都是一个</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">单向链表的头节点</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，如果发生哈希冲突（即两个不同的键经过哈希运算得到的数组索引位置相同），则将新的键值对添加到对应索引位置的链表中。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">链表节点包含</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">键</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">、</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">值</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">和</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">指向下一个节点的指针</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">。在Java 8之后，当链表长度达到一定</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">阈值时（默认为8）</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，链表会自动</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">转换为红黑树</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，以提高查找效率。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">红黑树是一种</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">自平衡的二叉搜索树</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，它的插入、删除和查找操作的时间复杂度都是</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">O(log n)</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，相比于链表，红黑树在查找效率上更高。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">通过哈希函数将键映射到数组索引位置，可以快速定位到对应的链表或红黑树，然后在链表或红黑树中进行查找、插入或删除操作。HashMap通过哈希表的数据结构，实现了高效的键值对存储和查找。</font>



> 更新: 2023-08-28 17:06:12  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xwyb50ph2lulal1c>