# ElasticSearch中的分片是什么

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">在Elasticsearch中，分片（shard）是将索引内部的数据分割成多个部分的机制，用于分布、存储和管理索引的数据。分片是Elasticsearch的核心概念，它们允许索引被拆分为多个物理或逻辑部分，以实现分布式存储和处理数据的能力。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">每个索引都可以配置分片数目，通常包括两种主要类型的分片：主分片（Primary Shard）和副本分片（Replica Shard）。</font>

1. **<font style="background-color:rgb(247, 247, 248);">主分片（Primary Shard）：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 主分片是索引数据的原始分片，每个索引至少有一个主分片。主分片负责接收文档的索引请求，并将数据存储在其上。主分片的数量在索引创建时定义，通常不能更改。</font>
2. **<font style="background-color:rgb(247, 247, 248);">副本分片（Replica Shard）：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 每个主分片可以有零个或多个副本分片。副本分片是主分片的完全复制，它们用于提高数据的可用性和查询性能。副本分片的数量可以在索引运行时动态调整。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">分片的概念使Elasticsearch能够具备以下关键特性：</font>

+ **<font style="background-color:rgb(247, 247, 248);">分布式存储：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 数据可以在集群的不同节点上分布存储，允许Elasticsearch处理大规模的数据。</font>
+ **<font style="background-color:rgb(247, 247, 248);">水平扩展：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 可以根据需要增加索引的分片数目，以适应不断增长的数据规模。</font>
+ **<font style="background-color:rgb(247, 247, 248);">高可用性：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 通过使用副本分片，Elasticsearch确保了数据的冗余备份，以应对节点故障和提供高可用性。</font>
+ **<font style="background-color:rgb(247, 247, 248);">并行查询：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 每个分片可以并行处理查询请求，从而提高了查询性能。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">总之，分片是Elasticsearch的关键组成部分，支持其在分布式环境中高效存储、检索和处理大量数据。</font>



> 更新: 2023-09-04 17:28:21  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/wp5sppd2wpi5c49e>