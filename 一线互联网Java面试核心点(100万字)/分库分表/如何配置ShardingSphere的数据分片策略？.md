# 如何配置ShardingSphere的数据分片策略？

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">在ShardingSphere中配置数据分片策略涉及到定义如何将数据分布到不同的数据库和表中，以满足分库分表的需求。通常按照以下步骤来配置一个数据分片策略：</font>

1. <font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">配置数据源：</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">在 ShardingSphere 的配置文件中，配置多个数据源，每个数据源对应一个数据库实例。</font>
2. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">配置逻辑表： 在配置文件中，配置多个逻辑表。每个逻辑表对应一个或多个真实数据表。</font>
3. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">配置逻辑表的主键生成策略：分库分表场景下，主键不能由数据库本地生成，所以通常会在 ShardingSphere 中配置主键生成策略，用来在分布式场景下，给逻辑表的每一条记录生成一个唯一的主键</font>
4. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">配置逻辑表的分库策略和分表策略：分别配置逻辑表的分库策略和分表策略。在配置策略时，一般先配置逻辑表的分片键，也就是按哪个字段分片。然后配置对应的分片算法，也就是按什么规则进行分片。常用的分片算法有取模算法、哈希算法等，也可以自定义复杂算法。</font>
5. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">补充一些其他规则：根据具体业务要求，配置一些补充的规则。例如敏感数据加密、广播表、绑定表、影子库等。</font>



> 更新: 2023-09-10 17:50:20  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rrd5uquhzddtbg8w>