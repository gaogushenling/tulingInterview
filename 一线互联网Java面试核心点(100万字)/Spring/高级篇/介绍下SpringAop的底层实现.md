# 介绍下SpringAop的底层实现

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Spring AOP是Spring框架的一个重要组成部分，用于实现面向切面编程。它通过在方法调用前、调用后或异常抛出时插入通知，允许开发者在核心业务逻辑之外执行横切关注点的代码。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">底层实现主要分两部分：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">创建AOP动态代理</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">和</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">调用代理</font>**

**<font style="background-color:rgb(247, 247, 248);">在启动Spring会</font>****<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">创建AOP动态代理</font>****<font style="background-color:rgb(247, 247, 248);">：</font>**

**<font style="background-color:rgb(247, 247, 248);">首先通过AspectJ解析切点表达式：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 在创建代理对象时，Spring AOP使用AspectJ来解析切点表达式。它会根据定义的条件匹配目标Bean的方法。如果Bean不符合切点的条件，将跳过，否则将会</font>**<font style="background-color:rgb(247, 247, 248);">通动态代理包装Bean对象：</font>**<font style="background-color:rgb(247, 247, 248);">具体会</font><font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">根据目标对象是否实现接口来选择使用JDK动态代理或CGLIB代理。这使得AOP可以适用于各种类型的目标对象。</font>

**<font style="background-color:rgb(247, 247, 248);">在调用阶段：</font>**

1. <font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> Spring AOP使用责任链模式来管理通知的执行顺序。通知拦截链包括前置通知、后置通知、异常通知、最终通知和环绕通知，它们按照配置的顺序形成链式结构。</font>
2. **<font style="background-color:rgb(247, 247, 248);">通知的有序执行：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 责任链确保通知按照预期顺序执行。前置通知在目标方法执行前执行，后置通知在目标方法成功执行后执行，异常通知在方法抛出异常时执行，最终通知无论如何都会执行，而环绕通知包裹目标方法，允许在方法执行前后添加额外的行为。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">综上所述，Spring AOP在创建启动阶段使用AspectJ解析切点表达式如果匹配使用动态代理，而在调用阶段使用责任链模式确保通知的有序执行。这些机制共同构成了Spring AOP的底层实现。</font>

<font style="color:rgb(0, 0, 0);">  
</font>



> 更新: 2023-09-07 16:15:57  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hqis2gtc02278paf>