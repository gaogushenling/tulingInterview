# 什么是ShardingSphere的广播表？

<font style="color:rgb(5, 7, 59);">ShardingSphere的广播表是指存在于每个分片数据源中的表。这些表的结构和数据在每个数据库中都完全一致。这种表适用于数据量不大且需要与海量数据的表进行关联查询的场景，例如字典表、省份信息等。对于广播表，ShardingSphere不会对数据进行分片，所有节点的数据都是完全一致的。当有新的插入、更新操作时，它们会实时在所有节点上执行，以保证各个分片的数据一致性。查询操作只需要从一个节点获取，而不是从多个节点获取。同时，广播表可以与任何一个表进行JOIN操作。在ShardingSphere中，可以通过创建广播表的方式来实现广播表的功能。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">在ShardingSphere中，开发人员可以配置广播表，使其在分片集群中自动生效。广播表的使用可以简化分布式系统中的数据管理，确保全局数据的一致性，同时降低了查询广播表的复杂性。</font>



> 更新: 2023-09-11 13:19:20  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ogb45izelbgbkngu>