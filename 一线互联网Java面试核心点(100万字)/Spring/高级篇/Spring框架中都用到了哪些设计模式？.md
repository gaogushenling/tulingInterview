# Spring 框架中都用到了哪些设计模式？

1. **<font style="background-color:rgb(247, 247, 248);">简单工厂：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">BeanFactory</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring的</font>**<font style="background-color:rgb(247, 247, 248);">BeanFactory</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">充当工厂，负责根据配置信息创建Bean实例。它是一种工厂模式的应用，根据指定的类名或ID创建Bean对象。</font>
2. **<font style="background-color:rgb(247, 247, 248);">工厂方法：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">FactoryBean</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：</font>**<font style="background-color:rgb(247, 247, 248);">FactoryBean</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">接口允许用户自定义Bean的创建逻辑，实现了工厂方法模式。开发人员可以使用</font>**<font style="background-color:rgb(247, 247, 248);">FactoryBean</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">来创建复杂的Bean实例。</font>
3. **<font style="background-color:rgb(247, 247, 248);">单例模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">Bean实例</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring默认将Bean配置为单例，确保在容器中只有一个共享的实例，这有助于节省资源和提高性能。</font>
4. **<font style="background-color:rgb(247, 247, 248);">适配器模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">SpringMVC中的HandlerAdapter</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：SpringMVC的</font>**<font style="background-color:rgb(247, 247, 248);">HandlerAdapter</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">允许不同类型的处理器适配到处理器接口，以实现统一的处理器调用。这是适配器模式的应用。</font>
5. **<font style="background-color:rgb(247, 247, 248);">装饰器模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">BeanWrapper</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring的</font>**<font style="background-color:rgb(247, 247, 248);">BeanWrapper</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">允许在不修改原始Bean类的情况下添加额外的功能，这是装饰器模式的实际应用。</font>
6. **<font style="background-color:rgb(247, 247, 248);">代理模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">AOP底层</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring的AOP（面向切面编程）底层通过代理模式来实现切面功能，包括JDK动态代理和CGLIB代理。</font>
7. **<font style="background-color:rgb(247, 247, 248);">观察者模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">Spring的事件监听</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring的事件监听机制是观察者模式的应用，它允许组件监听和响应特定类型的事件，实现了松耦合的组件通信。</font>
8. **<font style="background-color:rgb(247, 247, 248);">策略模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">excludeFilters、includeFilters</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring允许使用策略模式来定义包扫描时的过滤策略，如在</font>**<font style="background-color:rgb(247, 247, 248);">@ComponentScan</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">注解中使用的</font>**<font style="background-color:rgb(247, 247, 248);">excludeFilters</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">和</font>**<font style="background-color:rgb(247, 247, 248);">includeFilters</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">。</font>
9. **<font style="background-color:rgb(247, 247, 248);">模板方法模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">Spring几乎所有的外接扩展</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring框架的许多模块和外部扩展都采用模板方法模式，例如JdbcTemplate、HibernateTemplate等。</font>
10. **<font style="background-color:rgb(247, 247, 248);">责任链模式：</font>**
    - **<font style="background-color:rgb(247, 247, 248);">AOP的方法调用</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">：Spring AOP通过责任链模式实现通知（Advice）的调用，确保通知按顺序执行。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">Spring框架的设计哲学是通过这些设计模式来提供强大的功能和可定制性。它的模块化、松耦合的设计使得开发人员能够更轻松地构建可维护、可扩展和灵活的应用程序。这些设计模式的应用有助于实现代码重用、降低开发成本，是Spring框架广受欢迎的原因之一。</font>



> 更新: 2023-09-07 17:06:24  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/yc279v327deg3wvi>