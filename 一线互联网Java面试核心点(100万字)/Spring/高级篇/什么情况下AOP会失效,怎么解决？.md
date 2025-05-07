# 什么情况下AOP会失效,怎么解决？

<font style="color:rgb(55, 65, 81);">  
大部分失效是由于：</font>

1. **内部方法调用：**<font style="color:rgb(55, 65, 81);"> 如果在同一个类中的一个方法调用另一个方法，AOP通知可能不会触发，因为AOP通常是通过代理对象拦截外部方法调用的。解决方式是注入本类对象进行调用， 或者</font><font style="color:rgb(77, 77, 77);">设置暴露当前代理对象到本地线程， 可以通过</font>`<font style="color:rgb(77, 77, 77);">AopContext.currentProxy()</font>`<font style="color:rgb(77, 77, 77);"> 拿到当前正在调用的动态代理对象。</font>
2. **静态方法：**<font style="color:rgb(55, 65, 81);"> AOP通常无法拦截静态方法的调用，因为静态方法不是通过对象调用的。解决方法是将静态方法调用替换为实例方法调用，或者考虑其他技术来实现横切关注点。</font>
3. **AOP配置问题：**<font style="color:rgb(55, 65, 81);"> 错误的AOP配置可能导致通知不正确地应用于目标方法，或者在不希望的情况下应用。解决方法是仔细检查AOP配置，确保切点表达式和通知类型正确配置。</font>
4. **代理问题：**<font style="color:rgb(55, 65, 81);"> 如果代理对象不正确地创建或配置，AOP通知可能无法生效。解决方法是调试底层源码确保代理对象正确创建，并且AOP通知能够拦截代理对象的方法调用。</font>



> 更新: 2023-09-07 16:45:15  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ye11sbtyf4e0o3gx>