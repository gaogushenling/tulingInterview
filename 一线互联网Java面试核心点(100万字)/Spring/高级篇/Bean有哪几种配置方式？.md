# Bean有哪几种配置方式？

<font style="color:rgb(36, 41, 47);">在Spring框架中，有以下几种常见的Bean配置方式：</font>

1. <font style="color:rgb(36, 41, 47);">XML配置：使用XML文件来配置Bean，通过<bean>元素定义Bean的属性和依赖关系。可以使用Spring的XML命名空间和标签来简化配置。</font>
2. <font style="color:rgb(36, 41, 47);">注解配置：使用注解来配置Bean，通过在Bean类上添加注解，如@Component、@Service、@Repository等，来标识Bean的角色和作用。</font>
3. <font style="color:rgb(36, 41, 47);">JavaConfig方式：使用Java类来配置Bean，通过编写一个配置类，使用@Configuration注解标识，然后在方法上使用@Bean注解来定义Bean。</font>
4. <font style="color:rgb(36, 41, 47);">@Import：</font><font style="color:rgb(36, 41, 47);">@Import注解可以用于导入其他配置类，也可以用于导入其他普通类。当导入的是配置类时，被导入的配置类中定义的Bean会被纳入到当前配置类的上下文中；当导入的是普通类时，被导入的类本身会被当作一个Bean进行注册。</font>

这4种是最常用的，另外还有两种一些冷门的：

5. <font style="color:rgb(36, 41, 47);">Groovy配置：使用Groovy脚本来配置Bean，通过编写一个Groovy脚本文件，使用Spring的DSL（Domain Specific Language）来定义Bean。</font>
6. <font style="color:rgb(36, 41, 47);">JSR-330：</font><font style="color:rgb(77, 77, 77);">Spring提供了对JSR-330标准注释(</font>[<font style="color:rgb(36, 41, 47);">依赖注入</font>](https://so.csdn.net/so/search?q=%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5&spm=1001.2101.3001.7020)<font style="color:rgb(77, 77, 77);">)的支持，</font><font style="color:rgb(36, 41, 47);">可以使用</font><font style="color:rgb(86, 101, 115);background-color:rgb(246, 248, 250);">@Named 或 @ManagedBean替代</font><font style="color:rgb(36, 41, 47);">@Component，但是需要</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(250, 250, 250);">javax.inject</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(250, 250, 250);">依赖。</font>

<font style="color:rgb(36, 41, 47);">这些配置方式可以单独使用，也可以结合使用，根据项目需求和个人偏好选择适合的配置方式。</font>



> 更新: 2023-09-06 21:21:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/kguwdz5lfcw6kxsg>