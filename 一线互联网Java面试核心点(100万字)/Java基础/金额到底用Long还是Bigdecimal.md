# 金额到底用Long还是Bigdecimal

金额到底用Long还是Bigdecimal， 一直是一个有争议的话题：

![1706530855651-396388ef-45c6-4e34-b042-ef1ef942116a.png](./img/ZHaMYgjalY_6z9f7/1706530855651-396388ef-45c6-4e34-b042-ef1ef942116a-518781.png)



![1706530870745-dd744075-c10d-4491-983c-d9f6213cf9b9.png](./img/ZHaMYgjalY_6z9f7/1706530870745-dd744075-c10d-4491-983c-d9f6213cf9b9-733248.png)



![1706530889905-1fc70ee5-7323-4dc3-8acb-8781a3e2ac01.png](./img/ZHaMYgjalY_6z9f7/1706530889905-1fc70ee5-7323-4dc3-8acb-8781a3e2ac01-242913.png)

![1706530903753-2f4afd7d-00bb-4539-bca4-3a5324506fb7.png](./img/ZHaMYgjalY_6z9f7/1706530903753-2f4afd7d-00bb-4539-bca4-3a5324506fb7-969091.png)

![1706530911668-a185afe7-77c1-4eab-96d8-9175514cb02e.png](./img/ZHaMYgjalY_6z9f7/1706530911668-a185afe7-77c1-4eab-96d8-9175514cb02e-572434.png)

![1706530944031-6660cd59-40b8-448f-8422-dbbeedae4327.png](./img/ZHaMYgjalY_6z9f7/1706530944031-6660cd59-40b8-448f-8422-dbbeedae4327-833885.png)

![1706530970468-46a71bb9-ba30-40d5-ab04-54a1e5abff4e.png](./img/ZHaMYgjalY_6z9f7/1706530970468-46a71bb9-ba30-40d5-ab04-54a1e5abff4e-136680.png)







我来说说我的观点， 大家也可以评论区说说你的观点：



 首先float和double肯定是排除的，因为<font style="color:rgb(37, 41, 51);">它们内部使用科学计数法，转换二进制的时候有可能出现无限小数位的问题 </font>

![1706531677813-da8ab11d-7fbd-44c8-99fd-6e8449b33eb7.jpeg](./img/ZHaMYgjalY_6z9f7/1706531677813-da8ab11d-7fbd-44c8-99fd-6e8449b33eb7-911997.jpeg)



那么大家就会选择Long和<font style="color:rgb(37, 41, 51);">BigDecimal， Long类型在存储时(比如保留2位小数点)x100,  取出来/100。</font>

其实<font style="color:rgb(37, 41, 51);">本质都是一样的，都是避免使用浮点数进行表达，只是Long属于隐式设定小数点，BigDecimal属于显示设定小数点。</font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">那么这2种到底怎么选择呢？</font>

<font style="color:rgb(37, 41, 51);">我的建议是： 在代码层面用</font>**BigDecimal ，**数据库层面可视情况定

<font style="color:rgb(37, 41, 51);"> 首先long性能更好：</font>

+ <font style="color:rgb(55, 65, 81);">整数类型（如 </font>**long**<font style="color:rgb(55, 65, 81);">）通常在计算机硬件上的性能更好，因为它们的操作可以在硬件层面上更有效地执行。</font>
+ **BigDecimal**<font style="color:rgb(55, 65, 81);">  需要额外的空间和计算开销。</font>





<font style="color:rgb(37, 41, 51);"> </font>![1706533593289-04bb4fa9-ab67-45da-ae32-433843c76619.png](./img/ZHaMYgjalY_6z9f7/1706533593289-04bb4fa9-ab67-45da-ae32-433843c76619-385513.png)

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">阿里的java开发手册是推荐用分存储的，希望大家都能用Long存储分，照顾一下彼此的开发体验。  
</font><font style="color:rgb(37, 41, 51);">“8.【强制】任何货币金额，均以最小货币单位且为整型类型进行存储。”</font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">但是对于一些金融系统要求小数点位数要求比较多， 比如精确后六位，  如果每次存x1000000   那long类型的内存开销也荡然无存了也会降低可读性即易用性，   不如用Decimal。</font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);">所以数据库在需求阶段能确定小数点位数可以用long， 如果位数不确定，或者要求太精准可以用DECIMAL</font>

<font style="color:rgb(37, 41, 51);"></font>

<font style="color:rgb(37, 41, 51);"></font>



> 更新: 2024-06-21 14:17:02  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/shaltzo6mbn9h6g3>