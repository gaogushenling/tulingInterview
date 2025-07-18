# Mysql的可重复读解决了哪些问题

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">MySQL的可重复读隔离级别解决了以下几个问题：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">脏读（Dirty Read）：在可重复读隔离级别下，一个事务读取的数据只能是已经提交的数据，避免了脏读问题。其他未提交的事务对于当前事务是不可见的，保证读取到的数据是一致的和可靠的。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">不可重复读（Non-repeatable Read）：在可重复读隔离级别下，同一个事务内多次读取同一数据，得到的结果是一致的。其他事务对于当前事务进行修改操作时，不会影响当前事务已经读取的数据，保证了读操作的一致性。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">幻读（Phantom Read）：在可重复读隔离级别下，同一个事务内多次查询同一范围的数据，得到的结果集是一致的。其他事务对于当前事务进行插入或删除操作时，不会影响当前事务已经查询到的结果集，保证了查询操作的一致性。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">通过可重复读隔离级别，MySQL实现了多版本并发控制（MVCC），即每个事务在开始执行时会创建一个可见性视图（read view），该视图记录了事务开始时已经提交的数据。事务对于这个视图看到的数据是一致的，即使其他事务在此之后进行了修改或删除操作。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">需要注意的是，虽然可重复读隔离级别解决了脏读、不可重复读和幻读问题，但并不能解决所有的并发冲突。在一些特殊场景下，仍然会存在一些并发问题，例如死锁、更新丢失和悬浮更新等。因此，在使用可重复读隔离级别时，仍然需要注意并发控制和合理使用锁机制来避免这些问题的发生。</font>



> 更新: 2023-08-24 19:17:23  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/adl7w35utgohzs7a>