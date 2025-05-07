# MySQL 有哪些高可用方案

<font style="color:rgb(36, 41, 47);">MySQL的高可用方案主要有以下几种：</font>

1. **<font style="color:rgb(36, 41, 47);">主从复制：</font>**<font style="color:rgb(36, 41, 47);">这是最常见的高可用方案。主库负责处理写操作，并将数据变更记录到binlog日志。从库将主库的binlog复制到自己的中继日志，然后执行中继日志中的事件，以达到与主库数据一致的目的。当主库出现故障时，可以将从库提升为新的主库，实现服务的高可用。</font>
2. **<font style="color:rgb(36, 41, 47);">集群：</font>**<font style="color:rgb(36, 41, 47);">MySQL集群是一个高可用、高性能的数据库集群解决方案。它使用了共享无关的架构，可以在节点之间自动分割和复制数据，实现了数据的高可用和高性能。</font>



> 更新: 2023-08-31 15:53:24  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/gyuzgz6vy8ganpsu>