# Mysql是如何回滚事务的

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">MySQL使用了Undo Log（回滚日志）来实现事务的回滚操作。当一个事务需要回滚时，MySQL会根据事务的Undo Log来撤销对数据库的修改操作，将数据恢复到事务开始之前的状态。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">具体的回滚过程如下：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">事务回滚触发：当事务发生异常、被显式回滚或者被外部终止时，MySQL会触发事务的回滚操作。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">逆序回滚：MySQL按照事务执行的逆序进行回滚。也就是说，先回滚最近的事务，再回滚之前的事务。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">Undo Log的使用：MySQL通过Undo Log来实现事务的回滚。Undo Log记录了事务对数据库的修改操作，包括插入、删除和更新。在回滚时，MySQL会根据Undo Log中的信息，将修改的数据恢复到事务开始之前的状态。如果是更新操作，则可以使用Undo Log中的旧值来恢复数据。</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">数据恢复：通过Undo Log中记录的修改操作和旧值，MySQL将数据恢复到事务开始之前的状态。恢复过程会对相应的数据进行撤销、删除或者更新操作。</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">清除Undo Log：当事务回滚完成后，MySQL会清除相关的Undo Log。回滚后的数据已经恢复到了事务开始之前的状态，Undo Log不再需要记录。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">通过使用Undo Log，MySQL能够实现事务的回滚操作，保证了事务的一致性和持久性。当事务需要回滚时，MySQL会按照事务的逆序进行回滚操作，并根据Undo Log中的信息将数据恢复到事务开始之前的状态。这种机制确保了数据库的数据完整性，避免了错误或者异常操作对数据库造成不可逆的影响。</font>



> 更新: 2023-08-24 20:38:35  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hbrw3hgh7pzao0qh>