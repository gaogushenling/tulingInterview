# SpringBoot为什么默认使用CGLIB

**<font style="background-color:rgb(247, 247, 248);">SpringBoot默认使用CGLIB 原因如下：</font>**

+ **<font style="background-color:rgb(247, 247, 248);">无需接口：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> CGLIB能够代理那些没有实现接口的类，而JDK动态代理只能代理实现了接口的类。这使得Spring Boot可以更灵活地使用代理，而无需依赖于接口。</font>
+ **<font style="background-color:rgb(247, 247, 248);">AOP支持：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> Spring Boot广泛使用AOP（面向切面编程）来处理日志、事务、安全性等横切关注点。CGLIB更适合创建AOP代理，因为它可以代理普通的类而不仅仅是接口，在开发中如果通过反射获得代理目标方法的注解，如果用JDK动态代理将导致无法获取。</font>
+ **<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">可以代理本类方法</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：这意味着即使在同一个类中调用了另一个方法，仍然可以触发代理的行为。这对于某些特定的AOP需求非常有用，因为它允许您在同一类中的方法之间应用切面。这种能力被称为"自我调用"或"内部调用"的代理。</font>
+ **<font style="background-color:rgb(247, 247, 248);">方法调用性能：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 一旦代理对象创建完成，实际的方法调用性能可能会因代理方式而异。在JDK 1.8之后，JDK动态代理的方法调用性能相对较好，但CGLIB仍然可能更快，因为CGlib是直接调用父类方法即目标方法，无需像JDK代理还要通过反射进行内部方法栈调用才能到目标方法。</font>



> 更新: 2023-09-26 14:06:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/big13fgmk149wb5l>