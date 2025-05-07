# BeanFactory 和FactoryBean有什么区别？

+ `**<font style="color:rgb(51, 51, 51);">BeanFactory</font>**`<font style="color:rgb(36, 41, 47);">是Spring框架的核心接口之一，用于管理和获取Bean对象亦陈为bean的容器。</font><font style="color:rgb(51, 51, 51);">使用了简单工厂模式，提供getBean方法用来获取bean。 </font>
+ `**<font style="color:rgb(51, 51, 51);">FactoryBean</font>**`<font style="color:rgb(51, 51, 51);">是一个bean，但是它是一个特殊的bean。</font>

<font style="color:rgb(51, 51, 51);">它是一个接口，</font>**<font style="color:rgb(51, 51, 51);">他必须被一个bean</font>**<font style="color:rgb(51, 51, 51);">去实现。</font>

<font style="color:rgb(36, 41, 47);">FactoryBean接口定义了两个方法：</font>`<font style="color:rgb(36, 41, 47);">getObject()</font>`<font style="color:rgb(36, 41, 47);">和</font>`<font style="color:rgb(36, 41, 47);">getObjectType()</font>`<font style="color:rgb(36, 41, 47);">。</font>

<font style="color:rgb(36, 41, 47);">getObjectType()方法用于返回创建的Bean对象的类型</font><font style="color:rgb(51, 51, 51);">。</font>

<font style="color:rgb(36, 41, 47);">getObject()方法用于返回创建的Bean对象，最终该Bean对象会进行注入，MyBatis集成Spring时的那个SqlSessionFactoryBean就实现了FactoryBean， 最终通过</font>`<font style="color:rgb(36, 41, 47);">getObject()</font>`<font style="color:rgb(36, 41, 47);">将SqlSessionFactory注入到Ioc容器中。</font>

<font style="color:rgb(51, 51, 51);">所以FactoryBean不是一个普通的Bean，它会表现出工厂模式的样子,</font><font style="color:rgb(36, 41, 47);">可以自定义创建Bean对象的逻辑，可以在创建Bean对象之前进行一些额外的处理。</font>

<font style="color:rgb(51, 51, 51);">如果要获得FactoryBean类本身而非getObject()返回的Bean可以通过在BeanName前加“&”进行获取。</font>

<font style="color:rgb(51, 51, 51);"></font>

<font style="color:rgb(51, 51, 51);">        所以他们直接没有什么关系， 实在要扯上点关系那就是BeanFactory管理了</font><font style="color:rgb(36, 41, 47);">FactoryBean</font><font style="color:rgb(51, 51, 51);">，</font>





> 更新: 2023-09-06 20:49:40  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hhw8tg1drwp3yczn>