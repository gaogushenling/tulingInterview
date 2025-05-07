# Spring-AOP通知和执行顺序？

<font style="color:rgb(77, 77, 77);">Spring切面可以应用5种类型的通知：</font>

1. <font style="color:rgb(0, 0, 0);">前置通知：在目标方法被调用之前调用通知功能；</font>
2. <font style="color:rgb(0, 0, 0);">后置通知：在目标方法完成之后调用通知，此时不会关心方法的输出是什么；</font>
3. <font style="color:rgb(0, 0, 0);">返回通知：在目标方法成功执行之后调用通知；</font>
4. <font style="color:rgb(0, 0, 0);">异常通知：在目标方法抛出异常后调用通知；</font>
5. <font style="color:rgb(0, 0, 0);">环绕通知：通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。</font>



执行顺序：

**<font style="color:rgb(223, 64, 42);">Spring在5.2.7之前的执行顺序是：</font>**

![1693988036899-65592d6e-c91e-413b-bc15-aa2d6b0d6983.png](./img/sZF54XM7SHqvaVD4/1693988036899-65592d6e-c91e-413b-bc15-aa2d6b0d6983-739272.png)

**<font style="color:rgb(223, 64, 42);">Spring在5.2.7之后就改变的通知的执行顺序改为：</font>**

<font style="color:rgb(51, 51, 51);">1、正常执行：前置--->方法---->返回--->后置</font>

<font style="color:rgb(51, 51, 51);">2、异常执行：前置--->方法---->异常--->后置</font>

<font style="color:rgb(51, 51, 51);"></font>

<font style="color:rgb(51, 51, 51);"></font>



> 更新: 2023-09-17 15:12:19  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xfezqv82xny1nep5>