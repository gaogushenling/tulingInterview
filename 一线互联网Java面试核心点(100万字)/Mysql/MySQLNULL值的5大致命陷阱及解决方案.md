# MySQL NULL 值的 5 大致命陷阱及解决方案

<font style="color:rgba(6, 8, 31, 0.88);">在日常开发中，MySQL 数据库字段允许 NULL 值是一个常见的设计，但它也可能引发许多意想不到的问题。NULL 值的特殊性经常让开发者在查询、统计和逻辑判断中踩坑，甚至导致程序异常。</font>

<font style="color:rgba(6, 8, 31, 0.88);">本文将通过详细的案例，分析 NULL 值可能引发的 5 大问题，并提供实用的解决方案和最佳实践，帮助你彻底掌握 NULL 值的处理方法。</font>

---

## <font style="color:rgba(6, 8, 31, 0.88);">数据准备：快速复现问题</font>
<font style="color:rgba(6, 8, 31, 0.88);">为了验证 NULL 值引发的问题，我们需要先创建测试表并插入测试数据。以下是完整的 SQL 代码：</font>

```plsql
CREATE TABLE person (  
  id INT AUTO_INCREMENT PRIMARY KEY,  
  name VARCHAR(50) DEFAULT NULL,  
  mobile VARCHAR(20) DEFAULT NULL  
);  

CREATE TABLE employee (  
  id INT AUTO_INCREMENT PRIMARY KEY,  
  salary INT DEFAULT NULL  
);

INSERT INTO person (name, mobile) VALUES  
('Alice', '1234567890'),  
('Bob', '9876543210'),  
(NULL, '1234567890'),  
('Alice', NULL),  
('Java', '1111111111'),  
('Python', '2222222222'),  
('C++', '3333333333'),  
(NULL, NULL),  
('Java', NULL),  
(NULL, '4444444444');

INSERT INTO employee (salary) VALUES  
(1000),  
(2000),
(NULL);
```

---

## <font style="color:rgba(6, 8, 31, 0.88);">NULL 值引发的 5 大问题</font>
### <font style="color:rgba(6, 8, 31, 0.88);">一、COUNT/DISTINCT 数据丢失</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">问题描述</font>
`<font style="color:rgba(6, 8, 31, 0.88);">COUNT</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">是 MySQL 中常用的统计函数，但当字段值为 NULL 时，</font>`<font style="color:rgba(6, 8, 31, 0.88);">COUNT(column)</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">会忽略这些记录，导致统计结果不准确。例如：</font>

```plsql
SELECT COUNT(*) AS total_count, COUNT(name) AS name_count FROM person;
```

<font style="color:rgba(6, 8, 31, 0.88);">执行结果如下：</font>

| **<font style="color:rgba(6, 8, 31, 0.88);">total_count</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">name_count</font>** |
| --- | --- |
| <font style="color:rgba(6, 8, 31, 0.88);">10</font> | <font style="color:rgba(6, 8, 31, 0.88);">7</font> |


<font style="color:rgba(6, 8, 31, 0.88);">可以看到，</font>`<font style="color:rgba(6, 8, 31, 0.88);">COUNT(name)</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">忽略了</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">字段为 NULL 的记录，导致统计结果丢失。</font>

#### <font style="color:rgba(6, 8, 31, 0.88);">解决方案</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">使用</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">COUNT(*)</font>**`<font style="color:rgba(6, 8, 31, 0.88);">：统计所有记录，包括字段值为 NULL 的行。</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">扩展知识</font>**<font style="color:rgba(6, 8, 31, 0.88);">：避免使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">COUNT(常量)</font>`<font style="color:rgba(6, 8, 31, 0.88);">，如</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">COUNT(1)</font>`<font style="color:rgba(6, 8, 31, 0.88);">，因为它的行为与</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">COUNT(*)</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">一致，但可读性较差。</font>

---

#### <font style="color:rgba(6, 8, 31, 0.88);">问题描述</font>
<font style="color:rgba(6, 8, 31, 0.88);">当使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">COUNT(DISTINCT column1, column2)</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">时，如果任意一个字段值为 NULL，即使另一列有不同的值，查询结果也会忽略这些记录。例如：</font>

```plsql
SELECT COUNT(DISTINCT name, mobile) AS distinct_count FROM person;
```

<font style="color:rgba(6, 8, 31, 0.88);">执行结果如下：</font>

| **<font style="color:rgba(6, 8, 31, 0.88);">distinct_count</font>** |
| --- |
| <font style="color:rgba(6, 8, 31, 0.88);">5</font> |


<font style="color:rgba(6, 8, 31, 0.88);">尽管</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">mobile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">列中有多个不同的值，但由于</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">或</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">mobile</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">中存在 NULL，导致部分记录被忽略。</font>

#### <font style="color:rgba(6, 8, 31, 0.88);">解决方案</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">避免字段值为 NULL</font>**<font style="color:rgba(6, 8, 31, 0.88);">：在表设计时，尽量设置字段为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">，并为其指定默认值。</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">使用替代函数</font>**<font style="color:rgba(6, 8, 31, 0.88);">：通过</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IFNULL(column, default_value)</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">将 NULL 转换为默认值，例如：</font>

```plsql
SELECT COUNT(DISTINCT IFNULL(name, ''), IFNULL(mobile, '')) AS distinct_count FROM person;
```

---

### <font style="color:rgba(6, 8, 31, 0.88);">二、NULL 对比结果为未知（false）</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">问题描述</font>
<font style="color:rgba(6, 8, 31, 0.88);">在使用非等于查询（</font>`<font style="color:rgba(6, 8, 31, 0.88);"><></font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">或</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">!=</font>`<font style="color:rgba(6, 8, 31, 0.88);">）时，NULL 值的记录会被忽略。例如：</font>

```plsql
SELECT * FROM person WHERE name != 'Java';
```

<font style="color:rgba(6, 8, 31, 0.88);">执行结果如下：</font>

| **<font style="color:rgba(6, 8, 31, 0.88);">id</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">name</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">mobile</font>** |
| --- | --- | --- |
| <font style="color:rgba(6, 8, 31, 0.88);">1</font> | <font style="color:rgba(6, 8, 31, 0.88);">Alice</font> | <font style="color:rgba(6, 8, 31, 0.88);">1234567890</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">2</font> | <font style="color:rgba(6, 8, 31, 0.88);">Bob</font> | <font style="color:rgba(6, 8, 31, 0.88);">9876543210</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">4</font> | <font style="color:rgba(6, 8, 31, 0.88);">Alice</font> | <font style="color:rgba(6, 8, 31, 0.88);">NULL</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">6</font> | <font style="color:rgba(6, 8, 31, 0.88);">Python</font> | <font style="color:rgba(6, 8, 31, 0.88);">2222222222</font> |
| <font style="color:rgba(6, 8, 31, 0.88);">7</font> | <font style="color:rgba(6, 8, 31, 0.88);">C++</font> | <font style="color:rgba(6, 8, 31, 0.88);">3333333333</font> |


<font style="color:rgba(6, 8, 31, 0.88);">可以看到，</font>`<font style="color:rgba(6, 8, 31, 0.88);">name</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">为 NULL 的记录被忽略，导致查询结果不完整。</font>

#### <font style="color:rgba(6, 8, 31, 0.88);">解决方案</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">显式处理 NULL</font>**<font style="color:rgba(6, 8, 31, 0.88);">：在查询条件中加入对 NULL 的判断，例如：</font>

```plsql
SELECT * FROM person WHERE name != 'Java' OR name IS NULL;
```

---

### 三、NULL 值运算都为 NULL
#### 问题描述
在使用 NULL 值进行运算时，比如加减乘除，拼接等等 最终的结果都为 NULL

```plsql
SELECT id,salary + 1 FROM employee;
SELECT id,concat(name,'-baili') FROM person;
```

### <font style="color:rgba(6, 8, 31, 0.88);">四、聚合空指针异常</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">问题描述</font>
<font style="color:rgba(6, 8, 31, 0.88);">在使用聚合函数（如</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">SUM</font>`<font style="color:rgba(6, 8, 31, 0.88);">、</font>`<font style="color:rgba(6, 8, 31, 0.88);">AVG</font>`<font style="color:rgba(6, 8, 31, 0.88);">）时，如果字段值为 NULL，查询结果也会为 NULL，而不是预期的 0。这可能导致程序在处理结果时出现空指针异常。例如：</font>

```plsql
SELECT SUM(salary) AS total_salary FROM employee WHERE id >= 3;
```

<font style="color:rgba(6, 8, 31, 0.88);">执行结果如下：</font>

| **<font style="color:rgba(6, 8, 31, 0.88);">total_salary</font>** |
| --- |
| <font style="color:rgba(6, 8, 31, 0.88);">NULL</font> |


#### <font style="color:rgba(6, 8, 31, 0.88);">解决方案</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">使用</font>****<font style="color:rgba(6, 8, 31, 0.88);"> </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">IFNULL</font>**`**<font style="color:rgba(6, 8, 31, 0.88);"> </font>****<font style="color:rgba(6, 8, 31, 0.88);">函数</font>**<font style="color:rgba(6, 8, 31, 0.88);">：将 NULL 转换为 0，例如：</font>

```plsql
SELECT SUM(IFNULL(salary, 0)) AS total_salary FROM employee WHERE id >= 3;
```

<font style="color:rgba(6, 8, 31, 0.88);">执行结果为：</font>

| **<font style="color:rgba(6, 8, 31, 0.88);">total_salary</font>** |
| --- |
| <font style="color:rgba(6, 8, 31, 0.88);">0</font> |


---

### <font style="color:rgba(6, 8, 31, 0.88);">五、Group By Order By 会统计 NULL 值</font>
#### <font style="color:rgba(6, 8, 31, 0.88);">问题描述</font>
<font style="color:rgba(6, 8, 31, 0.88);">在使用 group by 与 order by 时，不会剔除 NULL，会将 NULL 作为最小值，例如：</font>

```plsql
SELECT * FROM person order by name desc;
```

#### <font style="color:rgba(6, 8, 31, 0.88);">解决方案</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">使用 </font>**`**<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>**`<font style="color:rgba(6, 8, 31, 0.88);">：剔除掉 NULL 记录，例如：</font>

```plsql
SELECT * FROM person where name is not null order by name desc;
```

---

## <font style="color:rgba(6, 8, 31, 0.88);">最佳实践</font>
1. **<font style="color:rgba(6, 8, 31, 0.88);">表设计时尽量避免 NULL 值</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">设置字段为</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">为字段指定默认值，例如空字符串</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">''</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">或 0。</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">查询时显式处理 NULL</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IFNULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">或</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">COALESCE</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">函数将 NULL 转换为默认值。</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">在查询条件中加入</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">或</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">IS NOT NULL</font>`<font style="color:rgba(6, 8, 31, 0.88);">。</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">遵循规范</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">根据阿里巴巴《Java 开发手册》的建议，优先使用</font><font style="color:rgba(6, 8, 31, 0.88);"> </font>`<font style="color:rgba(6, 8, 31, 0.88);">ISNULL(column)</font>`<font style="color:rgba(6, 8, 31, 0.88);"> </font><font style="color:rgba(6, 8, 31, 0.88);">判断 NULL 值，提升代码可读性和执行效率。</font>

---

## <font style="color:rgba(6, 8, 31, 0.88);">总结</font>
<font style="color:rgba(6, 8, 31, 0.88);">MySQL 中 NULL 值的处理是一个容易被忽视但又非常重要的问题。</font>

<font style="color:rgba(6, 8, 31, 0.88);">通过本文的分析和解决方案，希望你能更好地理解和应对 NULL 值问题。</font>

<font style="color:rgba(6, 8, 31, 0.88);">在实际开发中，尽量避免使用 NULL 值，并遵循最佳实践，才能让你的数据库设计更加健壮、高效。</font>



> 更新: 2025-02-24 19:18:47  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/lnx5i0hic8kezp5y>