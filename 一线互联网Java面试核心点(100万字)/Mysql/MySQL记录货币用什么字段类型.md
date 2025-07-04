# MySQL 记录货币用什么字段类型

在MySQL中记录货币金额，一般推荐使用DECIMAL字段类型。

DECIMAL字段类型用于存储精确的定点数值，可以指定总共的位数和小数点后的位数。这使得它非常适合用于存储货币金额，因为货币金额通常需要精确到小数点后几位。

以下是一个示例创建DECIMAL字段类型的语句：

```plsql
CREATE TABLE my_table (
   amount DECIMAL(18, 2)
);
```

上述语句创建了一个名为amount的DECIMAL字段，总共有18位，其中小数点后有2位。

使用DECIMAL字段类型的好处包括：

1. **精确性：**DECIMAL字段类型可以确保货币金额的精确性，避免由于浮点数运算带来的精度问题。
2. **可控性：**通过指定总位数和小数位数，可以精确控制存储的金额范围和精度。
3. **计算准确性：**DECIMAL字段类型支持数值计算，如加法、减法和乘法等，保证计算结果的准确性。

需要注意的是，DECIMAL字段类型占用的存储空间相对较大，因此在设计表结构时需要考虑存储和性能需求，合理选择DECIMAL字段的位数。另外，应根据具体业务需求和国际化要求，考虑货币符号和货币转换等问题。



> 更新: 2023-08-27 22:41:44  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hp83cf520z8e45du>