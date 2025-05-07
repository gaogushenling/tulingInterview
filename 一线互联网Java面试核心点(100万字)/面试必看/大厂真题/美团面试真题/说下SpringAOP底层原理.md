# 说下Spring AOP底层原理

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">Spring AOP全称是</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">面向切面编程</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，是Spring框架的一个重要特性，它通过在运行时动态地将额外的行为（如日志记录、事务管理等）织入到应用程序的特定方法或代码块中。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">底层原理主要基于</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">动态代理</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">和</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">字节码操作</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">来实现。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">对于动态代理Spring AOP使用了</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">JDK动态代理</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">和</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">CGLIB动态代理</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">两种方式来实现代理对象的创建。如果被代理的目标对象</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">实现了接口</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，Spring AOP将使用JDK动态代理来创建代理对象；如果目标对象</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">没有实现接口</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">，Spring AOP将使用CGLIB动态代理来创建代理对象。代理对象在运行时会拦截目标对象的方法调用，并在方法执行前后执行额外的逻辑。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">对于字节码操作，Spring AOP是通过在编译时或运行时</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">修改字节码</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">来实现对目标对象的增强。它使用AspectJ提供的编译器或者在运行时使用AspectJ的字节码增强器来修改字节码。通过修改字节码，Spring AOP可以在方法调用前后</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">插入额外的代码</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(248, 248, 248);">。</font>



> 更新: 2023-08-28 16:53:15  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ktxx6egnrskqmooq>