# Seata是什么？它的工作原理是什么？

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Seata是一款开源的分布式事务解决方案，它提供了一个简单、高性能和易于使用的分布式事务服务。Seata的工作原理是基于两阶段提交协议的演变，通过将业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源，然后通过异步提交的方式快速完成事务。在回滚阶段，Seata通过一阶段的回滚日志进行反向补偿。此外，Seata还提供了多种事务模式，包括AT、TCC、Saga和XA事务模式，以适应不同的业务场景。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">在Seata中，分布式事务的核心组件包括全局事务管理器（GTM）和分支事务管理器（BTM）。GTM负责协调和管理全局事务，而BTM则负责管理分支事务。在分布式环境中，每个应用节点都会与GTM和BTM交互，以确保全局事务的一致性和数据的一致性。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">具体来说，当一个应用启动一个全局事务时，GTM会为该事务分配一个全局事务ID，并将该ID发送给所有的应用节点。每个应用节点在执行本地事务时，都需要将全局事务ID与本地事务绑定，并将执行结果发送给BTM。如果某个节点出现故障，BTM会向GTM申请回滚全局事务，否则GTM会向所有节点广播提交指令。</font>



> 更新: 2023-09-11 13:45:30  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ri4rc7begwh2dbd6>