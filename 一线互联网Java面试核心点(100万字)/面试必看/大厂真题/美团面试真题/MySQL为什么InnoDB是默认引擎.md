# MySQL 为什么 InnoDB 是默认引擎

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">MySQL中的InnoDB引擎是默认引擎，主要基于以下几个原因：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">事务支持：InnoDB是MySQL唯一一个提供事务支持的引擎。事务是一组操作的集合，要么全部成功，要么全部失败，确保数据的一致性和可靠性。对于具有高并发读写需求的应用，如电子商务、银行等，事务的支持是非常重要的。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">锁机制：InnoDB引擎采用行级锁来控制并发访问，而不是表级锁。这个特性大大提高了并发性能，可以同时进行多个操作，而不会因为锁定整个表而导致其他操作被阻塞。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">外键约束：InnoDB支持外键约束，能够保证数据的一致性和完整性。外键是表与表之间的关联，可以确保引用表中的数据在主表中存在，避免了数据的异常和错误。</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">崩溃恢复：InnoDB具备崩溃恢复的能力，能够在数据库、服务器或系统发生故障时，恢复到崩溃前的状态。这是因为InnoDB引擎使用了redo日志和undo日志来记录数据的修改操作，确保数据的持久性。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">虽然InnoDB是默认引擎，但MySQL也支持其他引擎，如MyISAM、Memory等。选择合适的引擎需要根据具体应用的需求进行权衡和选择。</font>



> 更新: 2023-08-24 20:29:11  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ea6i8e7zsdfdzd6c>