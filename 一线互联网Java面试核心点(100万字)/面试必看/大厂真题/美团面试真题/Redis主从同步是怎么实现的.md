# Redis 主从同步是怎么实现的

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">在Redis中，主从同步是通过以下步骤来实现的：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">建立连接：从服务器（从节点）通过向主服务器（主节点）发送SYNC命令来与主服务器建立连接。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">快照同步：主服务器在收到SYNC命令后，会执行BGSAVE命令生成一个RDB持久化文件，并将该文件发送给从服务器进行全量复制。从服务器在接收到RDB文件后，会将其加载到内存中，完成对主服务器的快照同步。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">增量复制：主服务器会将自己接收到的写命令（包括SET、DEL等）记录在内存中的命令缓冲区中，并异步地将这些写命令发送给从服务器。从服务器接收到写命令后，会按照相同的顺序执行这些命令，从而达到与主服务器的数据一致性。</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">心跳检测与断线重连：主从节点之间会周期性地进行心跳检测，以检测连接的状态。如果发现从节点与主节点的连接中断，从节点会尝试重新建立连接，并重新进行同步操作，以确保数据的一致性。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">需要注意的是，Redis的主从同步是异步复制的方式，从节点并不直接参与主节点的写操作，因此在主从同步期间，从节点可能会有一定的数据延迟。此外，Redis还支持部分重同步（Partial Resynchronization）功能，在重启或者网络断连的情况下可以加快复制的速度。</font>



> 更新: 2023-08-24 20:52:55  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rean0kfok6g7elg3>