# 说下ConcurrentHashMap和Hashtable的异同点

**<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 和 </font>**<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 都是用于在多线程环境中存储和操作键值对的数据结构，但它们在实现和性能方面存在一些重要的异同点：</font>

**<font style="background-color:rgb(247, 247, 248);">相似点</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

1. **<font style="background-color:rgb(247, 247, 248);">线程安全性：</font>****<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 和 </font>**<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 都是线程安全的，可以在多个线程同时访问它们而不需要额外的同步措施。</font>
2. **<font style="background-color:rgb(247, 247, 248);">键值对存储：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 两者都以键值对的方式存储数据，允许通过键来查找和访问值。</font>

**<font style="background-color:rgb(247, 247, 248);">不同点</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>

1. **<font style="background-color:rgb(247, 247, 248);">同步策略：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 使用了更加精细的分段锁（Segment），每个分段可以看作一个小的独立的哈希表，不同分段之间可以并发操作，因此多线程访问不同分段的数据时不会阻塞。这使得在多线程环境中，</font>**<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 的性能更好，因为只有在访问相同分段的数据时才需要竞争锁。</font>
    - **<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 使用了全局锁，也就是在任何时刻只能有一个线程访问 </font>**<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 的数据，这导致了在高并发环境下性能较差。</font>
2. **<font style="background-color:rgb(247, 247, 248);">Null 键和值：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 不允许存储 null 键或 null 值。如果尝试存储 null 键或值，会抛出 </font>**<font style="background-color:rgb(247, 247, 248);">NullPointerException</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">。</font>
    - **<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 不允许存储 null 键和值。</font>
3. **<font style="background-color:rgb(247, 247, 248);">迭代器：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 提供了更强大的迭代器支持，允许在遍历时对集合进行修改，而不会抛出 </font>**<font style="background-color:rgb(247, 247, 248);">ConcurrentModificationException</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 异常。</font>
    - **<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 在迭代时对集合进行修改会抛出 </font>**<font style="background-color:rgb(247, 247, 248);">ConcurrentModificationException</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 异常。</font>
4. **<font style="background-color:rgb(247, 247, 248);">性能：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 通常在高并发环境下性能更好，因为它使用了分段锁，允许多个线程同时读取和写入不同分段的数据。</font>
    - **<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 的性能相对较差，因为它使用全局锁，只允许一个线程操作整个数据结构。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">总的来说，</font>**<font style="background-color:rgb(247, 247, 248);">ConcurrentHashMap</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 是在多线程环境中更常用的选择，因为它提供了更好的性能和灵活性，同时避免了 </font>**<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 中的一些限制和性能瓶颈。然而，在某些情况下，如果不需要特定的高并发性能要求，</font>**<font style="background-color:rgb(247, 247, 248);">Hashtable</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 仍然可以用作线程安全的数据结构。</font>



> 更新: 2024-03-08 17:37:31  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/cazo0ro75gxuh49k>