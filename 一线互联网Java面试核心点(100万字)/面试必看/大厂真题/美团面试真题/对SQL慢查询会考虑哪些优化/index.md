# 对 SQL 慢查询会考虑哪些优化

<font style="color:rgb(0,0,0);">1、分析语句，是否加载了不必要的字段/数据。 </font>

<font style="color:rgb(0,0,0);">2、分析 SQL 执行计划（explain extended），思考可能的优化点，是否命中索引等。 </font>

<font style="color:rgb(0,0,0);">3、查看 SQL 涉及的表结构和索引信息。 </font>

<font style="color:rgb(0,0,0);">4、如果 SQL 很复杂，优化 SQL 结构。 </font>

<font style="color:rgb(0,0,0);">5、按照可能的优化点执行表结构变更、增加索引、SQL 改写等操作。 </font>

<font style="color:rgb(0,0,0);">6、查看优化后的执行时间和执行计划。 </font>

<font style="color:rgb(0,0,0);">7、如果表数据量太大，考虑分表。 </font>

<font style="color:rgb(0,0,0);">8、利用缓存，减少查询次数。</font>



> 更新: 2024-06-06 15:39:03  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/oaarx2tzlroiwu99>