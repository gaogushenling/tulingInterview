# MySQL 中有哪几种锁？

在MySQL中，常见的锁包括以下几种：

1. **表级锁（Table-level Locking）**：在事务操作中对整个表进行加锁。当一个事务对表进行写入操作时，其他事务无法对该表进行任何读写操作。表级锁通常是针对特定的DDL操作或备份操作。
2. **共享锁（Shared Lock）**：也称为读锁（Read Lock），用于允许多个事务同时读取同一资源，但禁止并发写入操作。其他事务可以获取共享锁，但无法获取排他锁。
3. **排他锁（Exclusive Lock）**：也称为写锁（Write Lock），用于独占地锁定资源，阻止其他事务的读写操作。其他事务无法获取共享锁或排他锁，直到持有排他锁的事务释放锁。
4. **行级锁（Row-level Locking）**：也称为记录锁（Record Locking），在事务操作中对数据行进行加锁。行级锁可以控制并发读写操作，不同事务之间可以并发地访问不同行的数据。MySQL的InnoDB存储引擎默认使用行级锁。
5. **记录锁（Record Lock）**：用于行级锁的一种形式，锁定数据库中的一个记录（行）以保证事务的隔离性和完整性。
6. **间隙锁（Gap Lock）**：用于行级锁的一种形式，锁定两个记录之间的间隙。它可以防止其他事务在该间隙中插入新记录，从而保证数据的一致性。
7. **<font style="color:rgb(36, 41, 47);">临键锁（Next-Key Locks）：</font>**<font style="color:rgb(36, 41, 47);"> </font><font style="color:rgb(36, 41, 47);">临键锁是记录锁和间隙锁的结合，锁定的是一个范围，并且包括记录本身。</font>

需要注意的是，MySQL的不同存储引擎对锁的支持和实现方式可能有所不同。例如，MyISAM存储引擎使用表级锁来控制并发访问，而InnoDB存储引擎则支持更细粒度的行级锁，提供更好的并发性能和数据一致性。



> 更新: 2023-08-27 22:13:22  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ark9bak5ai6idatg>