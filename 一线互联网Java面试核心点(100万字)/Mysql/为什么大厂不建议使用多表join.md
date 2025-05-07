# 为什么大厂不建议使用多表join

<font style="color:rgba(0, 0, 0, 0.82);">在大型互联网企业或者“超大规模”数据库环境中，不建议频繁使用多表 JOIN 的原因主要涉及性能、可维护性和架构设计等方面。以下是一些常见的考虑因素：</font>

1. **<font style="color:rgba(0, 0, 0, 0.82);">性能问题</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">计算复杂性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：多表 JOIN 通常需要进行复杂的计算，如哈希连接、合并连接，这会显著增加查询的计算开销，影响响应时间。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">资源消耗</font>**<font style="color:rgba(0, 0, 0, 0.82);">：JOIN 操作特别是在多表参与时，会占用大量的内存和CPU资源，尤其是当涉及大表或需要对大量数据进行JOIN时。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">索引依赖</font>**<font style="color:rgba(0, 0, 0, 0.82);">：JOIN 性能很大程度上依赖于索引的存在和选择，当索引不当或者没有索引时，查询性能会大幅下降。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">可扩展性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">水平扩展挑战</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在分布式数据库架构中，JOIN 操作可能需要跨多个节点获取数据（即“跨节点JOIN”），这往往会导致大量的数据传输，增加系统负担。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">难以分片</font>**<font style="color:rgba(0, 0, 0, 0.82);">：复杂的 JOIN 查询在分片数据库中难以优化，因为涉及多个分片的数据可能位于不同的物理节点上，因此难以高效进行。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">维护与复杂度</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">代码复杂性</font>**<font style="color:rgba(0, 0, 0, 0.82);">：复杂的多表 JOIN 查询往往导致SQL语句难以理解和维护。维护这种复杂查询的成本较高。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">调试困难</font>**<font style="color:rgba(0, 0, 0, 0.82);">：当数据错误或者查询性能不佳时，复杂的 JOIN 查询更难以调试和定位问题。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">架构设计</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">领域模型割裂</font>**<font style="color:rgba(0, 0, 0, 0.82);">：JOIN 通常需要深度了解不同表之间的关系，容易导致领域模型设计上的不一致。</font>
    - **<font style="color:rgba(0, 0, 0, 0.82);">微服务架构限制</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在微服务架构中，数据通常是去中心化和跨服务的，JOIN 查询往往违反服务数据自治的原则。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">替代策略</font>
<font style="color:rgba(0, 0, 0, 0.82);">为了避免使用多表 JOIN，企业通常采取以下策略：</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">反范式化</font>**<font style="color:rgba(0, 0, 0, 0.82);">：适当的冗余存储以减少JOIN需求。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">数据冗余</font>**<font style="color:rgba(0, 0, 0, 0.82);">：在不同表中冗余存储一些公共字段，以避免频繁的JOIN。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">分而治之</font>**<font style="color:rgba(0, 0, 0, 0.82);">：将复杂查询拆分为多个简单查询，在应用层进行数据组合。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">预先计算</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过ETL作业预先计算和存储结果。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">使用NoSQL数据库</font>**<font style="color:rgba(0, 0, 0, 0.82);">：设计生产系统时采用文档数据库或键值存储一类的NoSQL数据库来轻松支持复杂数据需求。</font>

<font style="color:rgba(0, 0, 0, 0.82);">这些方法有助于提高查询性能、减少延迟并增强系统的可扩展性。因此，大厂在设计数据库和编写查询时，往往会尽量避免频繁和复杂的多表 JOIN。</font>

<font style="color:rgba(0, 0, 0, 0.82);"></font>

[bilibili](https://player.bilibili.com/player.html?bvid=BV1kFpue5Ehj&autoplay=0)



> 更新: 2024-09-13 19:57:29  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ovr4nuffqui6vz1b>