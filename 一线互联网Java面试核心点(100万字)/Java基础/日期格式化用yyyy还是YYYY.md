# 日期格式化用yyyy还是YYYY

一个工作6年的小伙伴日期时间格式化还在写大写YYYY，  你是真的没踩过雷啊！

<font style="color:#FFFFFF;">哈喽大家好我是徐庶！需要本视频文字版的小伙伴可以在评论区扣666，里面会有更详细的图和代码。</font>

如果你现在还在用大写YYYY格式化日期，那赶紧换成小写yyyy。

有什么区别？  踩过雷的我来告诉你！通常情况下可能没问题，但是**<font style="color:#DF2A3F;">在跨年的时候</font>**大概率就会有问题了。

你可以尝试把“2024-12-31 23:59:59”通过大写Y和小写Y进行格式化， 会发现大写Y的日期2024年变成了2025年，为什么会这样呢？



原因很简单， 大写的YYYY表示一个基于周的年份。它是根据周计算的年份，而小写的yyyy是基于日历的年份。

所以通常情况下，你就发现不了什么问题，但在**<font style="color:#DF2A3F;">跨年的第一周或最后一周</font>**可能会有差异。



这就是一个隐性雷了，赶紧去项目里面封装一个公共的日期处理类规范起来吧， 要不然下次来个小白又写大写Y了。

<font style="color:#FFFFFF;">好， 如果视频对你有帮助可以给徐庶老师一个三连， 我们下期见！/</font>



> 更新: 2024-06-21 11:49:03  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/gnygnz9z01kpugqg>