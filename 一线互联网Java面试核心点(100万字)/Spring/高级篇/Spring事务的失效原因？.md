# Spring事务的失效原因？

 

## <font style="color:rgb(0, 0, 0);">配置不对：</font>
1. <font style="color:rgb(0, 0, 0);">方法是private 也会失效，解决：改成public</font>
2. <font style="color:rgb(0, 0, 0);">目标类没有配置为Bean也会失效 解决：配置为Bean</font>
3. <font style="color:rgb(0, 0, 0);">自己捕获了异常 解决：不要捕获处理</font>
4. <font style="color:rgb(0, 0, 0);">使用cglib动态代理，但是@Transactional声明在接口上面</font>
5. <font style="color:rgb(0, 0, 0);">抛出了非RuntimeException异常 解决 ：通过rollbackfor指定回滚的异常</font>

## <font style="color:rgb(0, 0, 0);">不支持：</font>
<font style="color:rgb(247, 117, 103);">1. </font>**<font style="color:rgb(247, 117, 103);">内部方法调用导致事务传播失效.</font>**

 

<font style="color:rgb(77, 77, 77);">解决方式：必须走</font>**<font style="color:rgb(77, 77, 77);">代理， 重新拿到代理对象再次执行方法才能进行增强</font>**

+ **<font style="color:rgb(77, 77, 77);">在本类中注入当前的bean</font>**
+ **<font style="color:rgb(77, 77, 77);">设置暴露当前代理对象到本地线程， 可以通过</font>**<font style="color:rgb(0, 0, 0);">AopContext.currentProxy() 拿到当前正在调用的动态代理对象</font>

 @EnableAspectJAutoProxy(exposeProxy = true) 

**<font style="color:rgb(247, 117, 103);">2. 多线程事务.</font>**

 解决方式：  

+     通过编程式事务， 手动控制事务提交、回滚；
+     通过分布式事务思想：2PC\3PC\SAGA等等



> 更新: 2024-11-28 14:19:03  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/oq3yl04fehofd252>