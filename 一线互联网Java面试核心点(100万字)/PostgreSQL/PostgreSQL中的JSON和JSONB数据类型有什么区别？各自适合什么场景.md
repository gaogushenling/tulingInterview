# PostgreSQL中的JSON和JSONB数据类型有什么区别？各自适合什么场景

<font style="color:rgba(6, 8, 31, 0.88);">PostgreSQL中的JSON和JSONB是两种存储JSON数据的不同方式，它们各有特点和适用场景。下面我将详细比较这两种数据类型：</font>

### <font style="color:rgba(6, 8, 31, 0.88);">1. 存储方式差异</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">JSON类型</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">以原始文本形式存储</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">保留空格和格式</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">每次操作都需要重新解析</font>

```sql
CREATE TABLE user_profiles (  
  id SERIAL PRIMARY KEY,  
  profile JSON  
);  

INSERT INTO user_profiles (profile)   
VALUES ('{"name": "John", "age": 30, "skills": ["Java", "PostgreSQL"]}');
```

#### <font style="color:rgba(6, 8, 31, 0.88);">JSONB类型</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">以二进制形式存储</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">不保留空格和格式</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">预处理和压缩，后续操作更快</font>

```sql
CREATE TABLE user_profiles_jsonb (  
  id SERIAL PRIMARY KEY,  
  profile JSONB  
);  

INSERT INTO user_profiles_jsonb (profile)   
VALUES ('{"name": "John", "age": 30, "skills": ["Java", "PostgreSQL"]}');
```

### <font style="color:rgba(6, 8, 31, 0.88);">2. 性能和查询特点</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">JSON类型特点</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">保留原始输入格式</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">写入速度快</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">查询性能较低</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">适合：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">需要保留原始输入格式的场景</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">很少进行复杂查询的情况</font>

#### <font style="color:rgba(6, 8, 31, 0.88);">JSONB类型特点</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">支持索引</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">查询速度快</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">支持高效的部分检索</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">适合：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">需要频繁检索和过滤JSON数据</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">复杂查询场景</font>

### <font style="color:rgba(6, 8, 31, 0.88);">3. 查询示例</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">JSON查询</font>
```sql
-- JSON查询需要每次重sqs新解析  
SELECT profile->'name' FROM user_profiles   
WHERE profile->>'age' = '30';
```

#### <font style="color:rgba(6, 8, 31, 0.88);">JSONB查询</font>
```sql
-- JSONB查询更高效  
CREATE INDEX ON user_profiles_jsonb USING GIN (profile);  

SELECT profile->'name' FROM user_profiles_jsonb   
WHERE profile @> '{"age": 30}';  

-- 部分检索  
SELECT profile->>'skills'   
FROM user_profiles_jsonb   
WHERE profile->'skills' ? 'Java';
```

### <font style="color:rgba(6, 8, 31, 0.88);">4. 使用场景推荐</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">选择JSON的场景</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">日志存储</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">原始配置保留</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">不需要频繁检索的半结构化数据</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">需要保留输入的精确格式</font>

#### <font style="color:rgba(6, 8, 31, 0.88);">选择JSONB的场景</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">需要快速检索的文档型数据库</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">电商系统的产品属性</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">用户配置和动态属性</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">需要复杂查询和索引的应用</font>

### <font style="color:rgba(6, 8, 31, 0.88);">5. 性能和空间考虑</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">JSONB会略微增加存储空间</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">JSONB在大量写入时性能略低</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">JSONB在读取和复杂查询时性能显著优于JSON</font>

### <font style="color:rgba(6, 8, 31, 0.88);">推荐实践</font>
1. <font style="color:rgba(6, 8, 31, 0.88);">默认优先使用JSONB</font>
2. <font style="color:rgba(6, 8, 31, 0.88);">当保留原始格式很重要时使用JSON</font>
3. <font style="color:rgba(6, 8, 31, 0.88);">对于写入密集型场景，评估性能影响</font>
4. <font style="color:rgba(6, 8, 31, 0.88);">使用GIN索引优化JSONB查询性能</font>

### <font style="color:rgba(6, 8, 31, 0.88);">注意事项</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">PostgreSQL 9.4及以上版本完全支持这两种类型</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">谨慎处理类型转换，可能会丢失精度</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">复杂查询优先考虑JSONB</font>

<font style="color:rgba(6, 8, 31, 0.88);">总之，JSONB在大多数现代应用中是更好的选择，除非有特殊的格式保留需求。选择时需要根据具体业务场景权衡性能、存储和查询需求。</font>



> 更新: 2025-01-25 13:15:53  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xms10ysak8qeuolc>