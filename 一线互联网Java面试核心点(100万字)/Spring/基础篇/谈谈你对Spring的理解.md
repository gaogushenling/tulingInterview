# 谈谈你对Spring的理解

可以从2个层面理解Spring:

+ **<font style="color:rgb(0, 0, 0);">首先Spring是一个生态</font>**<font style="color:rgb(0, 0, 0);">：可以构建</font><font style="color:rgb(36, 41, 47);">企业级应用程序</font><font style="color:rgb(0, 0, 0);">所需的一切基础设施</font><font style="color:rgb(36, 41, 47);"> </font>
+ <font style="color:rgb(0, 0, 0);">但是，通常Spring指的就是Spring Framework，它有两大核心：</font>
1. **<font style="color:rgb(44, 62, 80);">IOC</font>**<font style="color:rgb(44, 62, 80);"> </font><font style="color:rgb(44, 62, 80);">和</font><font style="color:rgb(44, 62, 80);"> </font>**<font style="color:rgb(44, 62, 80);">DI</font>**<font style="color:rgb(44, 62, 80);"> </font><font style="color:rgb(44, 62, 80);">的支持</font>

<font style="color:rgb(44, 62, 80);">Spring 的核心就是一个大的工厂容器，可以维护所有对象的创建和依赖关系，Spring 工厂用于生成 Bean，并且管理 Bean 的生命周期，实现</font>**<font style="color:rgb(44, 62, 80);">高内聚低耦合</font>**<font style="color:rgb(44, 62, 80);">的设计理念。</font>

2. <font style="color:rgb(44, 62, 80);">AOP 编程的支持</font>

<font style="color:rgb(44, 62, 80);">Spring 提供了</font>**<font style="color:rgb(44, 62, 80);">面向切面编程</font>**<font style="color:rgb(44, 62, 80);">，</font><font style="color:rgb(36, 41, 47);">面向切面编程允许我们将横切关注点从核心业务逻辑中分离出来，实现代码的模块化和重用。</font><font style="color:rgb(44, 62, 80);">可以方便的实现对程序进行权限拦截、运行监控、日志记录等切面功能。</font>

<font style="color:rgb(44, 62, 80);"></font>

<font style="color:rgb(36, 41, 47);">除了这两大核心还提供了丰富的功能和模块， 数据访问、事务管理、Web开发等。数据访问模块提供了对数据库的访问支持，可以方便地进行数据库操作。事务管理模块提供了对事务的管理支持，确保数据的一致性和完整性。Web开发模块则提供了构建Web应用程序的工具和框架，简化了Web开发的过程。</font>

<font style="color:rgb(36, 41, 47);"></font>

<font style="color:rgb(44, 62, 80);">总结一句话：</font>**<font style="color:rgb(74, 74, 74);">它是一个轻量级、非入侵式的控制反转 (IoC) 和面向切面 (AOP) 的容器框架。</font>**

 



> 更新: 2023-09-06 14:36:04  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/raaan4q8yg1zulg3>