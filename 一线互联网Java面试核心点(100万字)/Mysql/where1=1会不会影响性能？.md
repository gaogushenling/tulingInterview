# where 1=1会不会影响性能？

那当sql的条件是动态的， 很多小伙伴会在where后面跟上1=1以保证语，经常看网上的八股文说1=1会影响性能， 建议用Mybatis的<where>标签.到底是不是这样的呢？



<font style="color:#FFFFFF;">哈喽大家好我是徐庶， 有需要跳槽面试的小伙伴可以再评论区扣666，我给你们发一份80万字的面试资料。</font>



那where 1=1 和 <where> 标签 两种方案，该如何选择？

+ 如果 MySQL Server版本小于 5.7，用了 MyBatis的话，建议使用<where> 标签。



+ 如果 MySQL版本大于等于 5.7，两个随便选；因为在MySQL5.7后，有一个所谓的<font style="color:rgb(37, 41, 51);">（常量折叠优化）可以在编译期消除重言式表达式。什么是重言式表达式，就是任何时候永远都为true的结果， 就会被优化器识别并优化掉，好奇的话你可以通过</font>`**<font style="color:rgb(51, 51, 51);">show</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(248, 248, 248);"> warnings;</font>`查看，就会发现1=1没有了。并且我也在一张<font style="color:rgb(37, 41, 51);">100多万的表里面把1=1 和<where>标签分别做了100次查询， 耗时时间相差无几。  所以5.7后两种方式随便选。</font>





   当然现在 MySQL Server版本基本都是 5.7以上了，不是的话那赶紧升级吧还是。<font style="color:#FFFFFF;">好如果视频对你有帮助可以一键三连哦， 我们下期见</font>

  


 



> 更新: 2024-06-20 15:57:27  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/syi7w8uxkfa1lstf>