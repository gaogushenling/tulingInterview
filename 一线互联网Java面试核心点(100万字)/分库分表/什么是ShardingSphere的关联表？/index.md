# 什么是 ShardingSphere 的关联表？

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">ShardingSphere提供了关联表的功能，主要解决在进行多表关联查询时，容易出现的查询效率太低的问题。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">具体来说，关联表定义了一种映射关系，将不同分片表的某些字段对应起来。这样，在进行多表关联查询时，ShardingSphere就可以通过这个映射关系，将查询操作转换成分片表的本地查询操作。这样就可以避免跨节点、跨数据库的查询，提高了查询效率。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">例如，如果有两个表：订单表(t_order)和订单情表表(t_order_item)，它们之间存在一个外键关联关系。我们可以定义一个关联规则，将这两个表关联起来。当进行多表关联查询时，ShardingSphere就会根据这个关联规则，自动将分片键相同的表关联起来进行查询，从而提高查询的效率。</font>



> 更新: 2023-09-11 13:29:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/mvcwgxi3x1q4l7zn>