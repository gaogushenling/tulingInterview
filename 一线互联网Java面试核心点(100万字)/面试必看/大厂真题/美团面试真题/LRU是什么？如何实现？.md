# LRU 是什么？如何实现？

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">LRU（Least Recently Used）是一种常见的缓存淘汰策略，它的基本思想是根据数据的访问时间来淘汰最近最少使用的数据。当缓存满了的时候，会将最近最少访问的数据从缓存中删除，以腾出空间给新的数据。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">在实现上，可以通过维护一个数据结构来记录数据的访问顺序，常用的数据结构是双向链表（Doubly Linked List）。链表的头部表示最近访问的数据，尾部表示最久未访问的数据。每次进行缓存访问时，如果数据在缓存中存在，则将其移到链表头部，表示最近访问过；如果不存在，则在链表头部插入新的数据。当缓存达到容量上限时，将链表尾部的数据删除即可。</font>



> 更新: 2023-08-24 20:24:04  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/yiv8g02dm3fwfip2>