# Spring Bean分类

<font style="color:rgb(6, 6, 7);">在Spring框架中，Bean是Spring容器管理的对象，它可以通过多种方式定义和配置。此问题可能换其他问法：比如你用过那些类型的Bean，你平时如何通过Spring创建Bean对象，单列Bean和原型Bean的区别等。</font>

<font style="color:rgb(6, 6, 7);">首先这个问题，可以根据不同的分类方式来回答。根据Bean的</font>**<font style="color:rgb(6, 6, 7);">作用域</font>**<font style="color:rgb(6, 6, 7);">、</font>**<font style="color:rgb(6, 6, 7);">定义方式</font>**<font style="color:rgb(6, 6, 7);">、</font>**<font style="color:rgb(6, 6, 7);">生命周期，功能，注入方式</font>**<font style="color:rgb(6, 6, 7);">特性，Spring中的Bean可以分为以下几种类型：</font>

---

### <font style="color:rgb(6, 6, 7);">1.</font><font style="color:rgb(6, 6, 7);"> </font>**<font style="color:rgb(6, 6, 7);">按作用域分类</font>**
<font style="color:rgb(6, 6, 7);">Spring中的Bean可以根据作用域（Scope）分为以下几种：</font>

#### **<font style="color:rgb(6, 6, 7);">（1）单例Bean（Singleton）</font>**
+ **<font style="color:rgb(6, 6, 7);">定义</font>**<font style="color:rgb(6, 6, 7);">：这是Spring的默认作用域。在整个Spring容器中，单例Bean只有一个实例。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">适合无状态的Bean（如工具类、服务类）。</font>
    - <font style="color:rgb(6, 6, 7);">性能较高，因为实例只创建一次。</font>
    - <font style="color:rgb(6, 6, 7);">Spring容器会管理其生命周期（创建、初始化、销毁）。</font>
+ **<font style="color:rgb(6, 6, 7);">适用场景</font>**<font style="color:rgb(6, 6, 7);">：大多数的业务逻辑类、工具类、服务类等。</font>
+ **<font style="color:rgb(6, 6, 7);">配置方式</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
public MyService myService() {
    return new MyService();
}
```

<font style="color:rgb(6, 6, 7);">或者通过XML配置：</font><font style="color:rgb(6, 6, 7);">xml</font>**复制**

```xml
<bean id="myService" class="com.example.MyService" scope="singleton"/>
```

---

#### **<font style="color:rgb(6, 6, 7);">（2）原型Bean（Prototype）</font>**
+ **<font style="color:rgb(6, 6, 7);">定义</font>**<font style="color:rgb(6, 6, 7);">：每次请求都会创建一个新的Bean实例。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">每次调用都会返回一个全新的实例。</font>
    - <font style="color:rgb(6, 6, 7);">适合有状态的Bean（如用户会话相关的Bean）。</font>
    - <font style="color:rgb(6, 6, 7);">Spring容器只负责创建实例，不会管理其生命周期。</font>
+ **<font style="color:rgb(6, 6, 7);">适用场景</font>**<font style="color:rgb(6, 6, 7);">：需要独立状态的Bean，如用户会话管理、临时对象等。</font>
+ **<font style="color:rgb(6, 6, 7);">配置方式</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
@Scope("prototype")
public MyPrototypeBean myPrototypeBean() {
    return new MyPrototypeBean();
}
```

<font style="color:rgb(6, 6, 7);">或者通过XML配置：</font><font style="color:rgb(6, 6, 7);">xml</font>**复制**

```xml
<bean id="myPrototypeBean" class="com.example.MyPrototypeBean" scope="prototype"/>
```

---

#### **<font style="color:rgb(6, 6, 7);">（3）会话Bean（Session）</font>**
+ **<font style="color:rgb(6, 6, 7);">定义</font>**<font style="color:rgb(6, 6, 7);">：作用域与HTTP会话绑定。每个HTTP会话都有一个独立的Bean实例。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">适用于Web应用中的用户会话相关数据。</font>
    - <font style="color:rgb(6, 6, 7);">实例的生命周期与会话绑定，会话结束时Bean销毁。</font>
+ **<font style="color:rgb(6, 6, 7);">适用场景</font>**<font style="color:rgb(6, 6, 7);">：用户会话管理、购物车等。</font>
+ **<font style="color:rgb(6, 6, 7);">配置方式</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
@Scope("session")
public UserSession userSession() {
    return new UserSession();
}
```

---

#### **<font style="color:rgb(6, 6, 7);">（4）请求Bean（Request）</font>**
+ **<font style="color:rgb(6, 6, 7);">定义</font>**<font style="color:rgb(6, 6, 7);">：作用域与HTTP请求绑定。每个HTTP请求都有一个独立的Bean实例。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">适用于每个请求都需要独立状态的场景。</font>
    - <font style="color:rgb(6, 6, 7);">请求结束时，Bean实例销毁。</font>
+ **<font style="color:rgb(6, 6, 7);">适用场景</font>**<font style="color:rgb(6, 6, 7);">：用户请求相关的数据处理。</font>
+ **<font style="color:rgb(6, 6, 7);">配置方式</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
@Scope("request")
public RequestData requestData() {
    return new RequestData();
}
```

---

#### **<font style="color:rgb(6, 6, 7);">（5）全局会话Bean（Global Session）</font>**
+ **<font style="color:rgb(6, 6, 7);">定义</font>**<font style="color:rgb(6, 6, 7);">：作用域与Portlet应用的全局会话绑定。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：</font>
    - <font style="color:rgb(6, 6, 7);">主要用于Portlet应用。</font>
    - <font style="color:rgb(6, 6, 7);">与会话Bean类似，但作用范围更广。</font>
+ **<font style="color:rgb(6, 6, 7);">适用场景</font>**<font style="color:rgb(6, 6, 7);">：Portlet应用中的全局会话管理。</font>
+ **<font style="color:rgb(6, 6, 7);">配置方式</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
@Scope("globalSession")
public GlobalSessionData globalSessionData() {
    return new GlobalSessionData();
}
```

---

### <font style="color:rgb(6, 6, 7);">2.</font><font style="color:rgb(6, 6, 7);"> </font>**<font style="color:rgb(6, 6, 7);">按定义方式分类</font>**
<font style="color:rgb(6, 6, 7);">Spring中的Bean可以通过以下几种方式定义：</font>

#### **<font style="color:rgb(6, 6, 7);">（1）基于注解的Bean</font>**
+ **<font style="color:rgb(6, 6, 7);">@Component</font>**<font style="color:rgb(6, 6, 7);">：通用组件注解，自动检测并注册为Bean。</font>
+ **<font style="color:rgb(6, 6, 7);">@Service</font>**<font style="color:rgb(6, 6, 7);">：用于服务层组件。</font>
+ **<font style="color:rgb(6, 6, 7);">@Repository</font>**<font style="color:rgb(6, 6, 7);">：用于数据访问层组件。</font>
+ **<font style="color:rgb(6, 6, 7);">@Controller</font>**<font style="color:rgb(6, 6, 7);">：用于Web控制器组件。</font>
+ **<font style="color:rgb(6, 6, 7);">@Bean</font>**<font style="color:rgb(6, 6, 7);">：在配置类中显式定义Bean。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Service
public class MyService {
    public void doSomething() {
        System.out.println("Doing something...");
    }
}
```

---

#### **<font style="color:rgb(6, 6, 7);">（2）基于XML配置的Bean</font>**
+ <font style="color:rgb(6, 6, 7);">在Spring的XML配置文件中定义Bean。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">xml</font>**复制**

```xml
<bean id="myBean" class="com.example.MyBean"/>
```

---

#### **<font style="color:rgb(6, 6, 7);">（3）基于Java配置的Bean</font>**
+ <font style="color:rgb(6, 6, 7);">使用</font>`@Configuration`<font style="color:rgb(6, 6, 7);">注解的类中定义Bean。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Configuration
public class AppConfig {
    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```

---

### <font style="color:rgb(6, 6, 7);">3.</font><font style="color:rgb(6, 6, 7);"> </font>**<font style="color:rgb(6, 6, 7);">按生命周期分类</font>**
<font style="color:rgb(6, 6, 7);">根据Bean的生命周期管理方式，可以分为以下几种：</font>

#### **<font style="color:rgb(6, 6, 7);">（1）普通Bean</font>**
+ <font style="color:rgb(6, 6, 7);">生命周期完全由Spring容器管理（创建、初始化、销毁）。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：单例Bean。</font>

---

#### **<font style="color:rgb(6, 6, 7);">（2）原型Bean</font>**
+ <font style="color:rgb(6, 6, 7);">Spring只负责创建实例，销毁由客户端管理。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：每次请求创建一个新实例，生命周期较短。</font>

---

#### **<font style="color:rgb(6, 6, 7);">（3）作用域代理Bean（Scoped Proxy）</font>**
+ <font style="color:rgb(6, 6, 7);">用于解决非单例Bean（如原型Bean、会话Bean）注入到单例Bean中的问题。</font>
+ <font style="color:rgb(6, 6, 7);">Spring会创建一个代理对象，代理对象会根据作用域动态获取目标Bean实例。</font>
+ **<font style="color:rgb(6, 6, 7);">配置方式</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
@Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
public MyPrototypeBean myPrototypeBean() {
    return new MyPrototypeBean();
}
```

---

### <font style="color:rgb(6, 6, 7);">4.</font><font style="color:rgb(6, 6, 7);"> </font>**<font style="color:rgb(6, 6, 7);">按功能分类</font>**
<font style="color:rgb(6, 6, 7);">根据Bean的功能和用途，可以分为以下几种：</font>

#### **<font style="color:rgb(6, 6, 7);">（1）普通业务Bean</font>**
+ <font style="color:rgb(6, 6, 7);">用于实现具体业务逻辑。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：服务类、工具类。</font>

---

#### **<font style="color:rgb(6, 6, 7);">（2）配置Bean</font>**
+ <font style="color:rgb(6, 6, 7);">用于定义配置信息，如数据源、事务管理器等。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
@Bean
public DataSource dataSource() {
    return new DriverManagerDataSource("jdbc:mysql://localhost:3306/mydb");
}
```

---

#### **<font style="color:rgb(6, 6, 7);">（3）工厂Bean（Factory Bean）</font>**
+ <font style="color:rgb(6, 6, 7);">实现</font>`FactoryBean`<font style="color:rgb(6, 6, 7);">接口，用于动态创建Bean。</font>
+ **<font style="color:rgb(6, 6, 7);">特点</font>**<font style="color:rgb(6, 6, 7);">：可以控制Bean的创建逻辑。</font>
+ **<font style="color:rgb(6, 6, 7);">示例</font>**<font style="color:rgb(6, 6, 7);">：</font><font style="color:rgb(6, 6, 7);">java</font>**复制**

```java
public class MyFactoryBean implements FactoryBean<MyBean> {
    @Override
    public MyBean getObject() {
        return new MyBean();
    }

    @Override
    public Class<?> getObjectType() {
        return MyBean.class;
    }
}
```

---

### <font style="color:rgb(6, 6, 7);">5.</font><font style="color:rgb(6, 6, 7);"> </font>**<font style="color:rgb(6, 6, 7);">按依赖注入方式分类</font>**
<font style="color:rgb(6, 6, 7);">根据依赖注入的方式，Bean可以分为以下几种：</font>

#### **<font style="color:rgb(6, 6, 7);">（1）构造器注入Bean</font>**
+ <font style="color:rgb(6, 6, 7);">通过构造器注入依赖。</font>
+ **<font style="color:rgb(6, 6, 7);">优点</font>**<font style="color:rgb(6, 6, 7);">：不可变性，依赖关系明确。</font>
+ **<font style="color:rgb(6, 6, 7);">缺点</font>**<font style="color:rgb(6, 6, 7);">：无法解决循环依赖。</font>

---

#### **<font style="color:rgb(6, 6, 7);">（2）Setter注入Bean</font>**
+ <font style="color:rgb(6, 6, 7);">通过Setter方法注入依赖。</font>
+ **<font style="color:rgb(6, 6, 7);">优点</font>**<font style="color:rgb(6, 6, 7);">：可以解决循环依赖。</font>
+ **<font style="color:rgb(6, 6, 7);">缺点</font>**<font style="color:rgb(6, 6, 7);">：依赖关系可变。</font>

---

### <font style="color:rgb(6, 6, 7);">总结</font>
<font style="color:rgb(6, 6, 7);">Spring中的Bean可以根据作用域、定义方式、生命周期、功能和依赖注入方式等多种维度进行分类。在实际开发中，选择合适的Bean类型和定义方式需要根据具体需求来决定。例如：</font>

+ **<font style="color:rgb(6, 6, 7);">单例Bean</font>**<font style="color:rgb(6, 6, 7);">：适合无状态的工具类和服务类。</font>
+ **<font style="color:rgb(6, 6, 7);">原型Bean</font>**<font style="color:rgb(6, 6, 7);">：适合有状态的Bean，如用户会话相关数据。</font>
+ **<font style="color:rgb(6, 6, 7);">注解方式</font>**<font style="color:rgb(6, 6, 7);">：适合现代Spring应用，代码更简洁。</font>
+ **<font style="color:rgb(6, 6, 7);">XML配置</font>**<font style="color:rgb(6, 6, 7);">：适合复杂的配置场景，便于管理。</font>

<font style="color:rgb(6, 6, 7);">通过合理使用这些Bean类型，可以更好地利用Spring框架的强大功能，实现高效、灵活的开发。</font>



> 更新: 2025-02-24 19:21:05  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/eg79g2gz7nl9ydbm>