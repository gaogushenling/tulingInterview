# HashMap的扩容机制是怎样的

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">HashMap的扩容机制是为了保持</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">负载因子</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">在一定范围内，以提高HashMap的性能和效率。负载因子是指HashMap中已存储的</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">键值对数量与数组容量的比值</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">当HashMap中的键值对数量超过了负载因子与数组容量的乘积时，就会触发扩容操作。默认情况下，</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">负载因子为0.75</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，即当键值对数量超过数组容量的75%时，会进行扩容。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">HashMap的扩容操作主要包括以下几个步骤：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">创建一个新的数组，其容量是原数组的</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">两倍</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">将原数组中的键值对重新分配到新数组中的对应位置。这个过程需要重新计算键的哈希值，并根据新数组的容量进行取模运算，以确定键值对在新数组中的位置。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">如果原数组中的某个位置有多个键值对，可能会出现</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">哈希冲突</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">。在重新分配键值对时，会使用链表或红黑树来解决冲突。</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">将新数组设置为HashMap的内部数组，并更新相关的属性，如</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">容量</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">和</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">阈值</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">通过扩容操作，HashMap可以动态调整数组的大小，以适应存储键值对数量的变化。这样可以保持负载因子在合适的范围内，避免链表或红黑树过长，从而保持HashMap的性能和效率。需要注意的是，扩容操作可能会导致一定的</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">性能损耗</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，因为需要重新计算哈希值和重新分配键值对。因此，在设计使用HashMap的时候，需要合理设置初始容量和负载因子，以减少扩容的频率。</font>



> 更新: 2023-08-28 19:14:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/qdcazqpoazu0eaa7>