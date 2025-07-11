# 什么是Seata？谈谈你对Seata的理解

**Seata是一款开源的分布式事务解决方案**，它主要用于解决在分布式系统中全局事务的一致性问题。

在分布式系统中，由于一次业务操作需要跨多个数据源或进行远程调用，往往会产生分布式事务问题。例如，在一个电商微服务系统中，订单服务和库存服务需要协同工作，如果订单服务已经创建成功，但库存服务因为某些原因失败了，就会导致数据不一致的问题。Seata就是为解决这个问题而产生的。

Seata的主要特点是无侵入以及高性能。它对业务无侵入，可以减少技术架构上的微服务化所带来的分布式事务问题对业务的侵入，同时高性能则是减少分布式事务解决方案所带来的性能消耗。

在Seata的事务处理中主要有三个重要的角色：事务的协调者（TC）、事务的管理者（TM）和事务的作业管理器（RM）。

+ **事务协调者（TC）**主要负责管理全局的分支事务的状态，用于全局性事务的提交和回滚。它会对所有的分支事务进行注册，然后根据各个分支事务的状态来决定是否提交或者回滚全局事务。
+ **事务管理者（TM）**用于开启、提交或回滚事务。它会根据业务逻辑来决定何时开启一个新的事务，并在适当的时候提交或回滚该事务。
+ **资源管理器（RM）**用于分支事务上的资源管理，向TC注册分支事务，上报分支事务的状态，接收TC的命令来提交或者回滚分支事务。



> 更新: 2023-09-11 14:46:09  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/blhhye687a58pxq5>