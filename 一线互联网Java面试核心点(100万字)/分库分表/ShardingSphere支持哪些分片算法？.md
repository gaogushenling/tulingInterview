# ShardingSphere支持哪些分片算法？

<font style="color:rgb(5, 7, 59);">ShardingSphere支持多种分片算法，主要包括：</font>

1. <font style="color:rgb(5, 7, 59);">精确分片算法（PreciseShardingAlgorithm）：用于处理使用单一键作为分片键的=与IN进行分片的场景。</font>
2. <font style="color:rgb(5, 7, 59);">范围分片算法（RangeShardingAlgorithm）：用于处理使用单一键作为分片键的BETWEEN AND、>、<、>=、<=进行分片的场景。</font>
3. <font style="color:rgb(5, 7, 59);">复合分片算法（ComplexKeysShardingAlgorithm）：用于处理使用多键作为分片键进行分片的场景，多个分片键的逻辑较复杂，需要应用开发者自行处理其中的复杂度。</font>
4. <font style="color:rgb(5, 7, 59);">提示分片算法（HintShardingAlgorithm）：用于处理分片规则与 SQL 无关的场景。对于分片字段非SQL决定，而由其他外置条件决定的场景，可使用SQL Hint灵活的注入分片字段。</font>

<font style="color:rgb(5, 7, 59);">此外，随着版本不断演进，ShardingSphere还在不断丰富分片算法，例如基于分片边界的范围分片算法（BoundaryBasedRangeShardingAlgorithm）、基于分片容量的范围分片算法（VolumeBasedRangeShardingAlgorithm）等。</font>



> 更新: 2023-09-11 13:32:39  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/qvent10nv2kb2rq6>