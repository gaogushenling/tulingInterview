# BeanFactory与FactoryBean区别：面试必备解析

<font style="color:rgba(6, 8, 31, 0.88);">今天跟大家分享一道Spring面试高频题：</font>**<font style="color:rgba(6, 8, 31, 0.88);">BeanFactory与FactoryBean的区别</font>**<font style="color:rgba(6, 8, 31, 0.88);">。</font>

<font style="color:rgba(6, 8, 31, 0.88);">这个问题在中高级Java面试中出现频率非常高，很多小伙伴常因为名字相似而搞混，导致面试时回答不清晰。</font>

<font style="color:rgba(6, 8, 31, 0.88);">下面，我将以最清晰直观的方式为大家讲解如何完美回答这个问题。</font>

## <font style="color:rgba(6, 8, 31, 0.88);">一、核心区别一句话总结</font>
**<font style="color:rgba(6, 8, 31, 0.88);">最简单记忆</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>

+ **<font style="color:rgba(6, 8, 31, 0.88);">BeanFactory</font>**<font style="color:rgba(6, 8, 31, 0.88);">是工厂，</font>**<font style="color:rgba(6, 8, 31, 0.88);">管理Bean</font>**<font style="color:rgba(6, 8, 31, 0.88);">的容器</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">FactoryBean</font>**<font style="color:rgba(6, 8, 31, 0.88);">是特殊Bean，</font>**<font style="color:rgba(6, 8, 31, 0.88);">生产Bean</font>**<font style="color:rgba(6, 8, 31, 0.88);">的工厂</font>

<font style="color:rgba(6, 8, 31, 0.88);">这个区别虽然简单，但面试时一定要表达清楚，接下来我们深入分析。</font>

## <font style="color:rgba(6, 8, 31, 0.88);">二、BeanFactory详解</font>
### <font style="color:rgba(6, 8, 31, 0.88);">1. 本质与定位</font>
<font style="color:rgba(6, 8, 31, 0.88);">BeanFactory是Spring框架的</font>**<font style="color:rgba(6, 8, 31, 0.88);">核心接口</font>**<font style="color:rgba(6, 8, 31, 0.88);">，是IoC容器的顶层设计。它定义了容器的基本功能，负责</font>**<font style="color:rgba(6, 8, 31, 0.88);">管理、装配和提供Bean</font>**<font style="color:rgba(6, 8, 31, 0.88);">。可以类比为一个大型工厂，负责生产和管理各种产品(Bean)。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">2. 主要功能</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">提供Bean的</font>**<font style="color:rgba(6, 8, 31, 0.88);">生命周期管理</font>**
+ <font style="color:rgba(6, 8, 31, 0.88);">实现</font>**<font style="color:rgba(6, 8, 31, 0.88);">依赖注入</font>**
+ <font style="color:rgba(6, 8, 31, 0.88);">支持</font>**<font style="color:rgba(6, 8, 31, 0.88);">Bean的装配</font>**<font style="color:rgba(6, 8, 31, 0.88);">和</font>**<font style="color:rgba(6, 8, 31, 0.88);">延迟初始化</font>**

### <font style="color:rgba(6, 8, 31, 0.88);">3. 常见实现</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">DefaultListableBeanFactory</font>**<font style="color:rgba(6, 8, 31, 0.88);">：最常用的实现类</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">XmlBeanFactory</font>**<font style="color:rgba(6, 8, 31, 0.88);">：读取XML配置文件的实现(已过时)</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">ApplicationContext</font>**<font style="color:rgba(6, 8, 31, 0.88);">：更高级的容器，扩展了BeanFactory</font>

```java
// BeanFactory的基本使用  
BeanFactory factory = new ClassPathXmlApplicationContext("applicationContext.xml");  
UserService userService = factory.getBean("userService", UserService.class);
```

## <font style="color:rgba(6, 8, 31, 0.88);">三、FactoryBean详解</font>
### <font style="color:rgba(6, 8, 31, 0.88);">1. 本质与定位</font>
<font style="color:rgba(6, 8, 31, 0.88);">FactoryBean是一个</font>**<font style="color:rgba(6, 8, 31, 0.88);">特殊的Bean</font>**<font style="color:rgba(6, 8, 31, 0.88);">，它是一个接口，用于</font>**<font style="color:rgba(6, 8, 31, 0.88);">定制化Bean的创建逻辑</font>**<font style="color:rgba(6, 8, 31, 0.88);">。当某些Bean的实例化过程复杂时，实现这个接口可以将复杂创建过程封装起来。</font>

### <font style="color:rgba(6, 8, 31, 0.88);">2. 核心方法</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">getObject()</font>**<font style="color:rgba(6, 8, 31, 0.88);">：返回该工厂创建的对象实例</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">getObjectType()</font>**<font style="color:rgba(6, 8, 31, 0.88);">：返回该工厂创建的对象类型</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">isSingleton()</font>**<font style="color:rgba(6, 8, 31, 0.88);">：返回由该工厂创建的对象是否是单例</font>

### <font style="color:rgba(6, 8, 31, 0.88);">3. 使用场景</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">复杂对象的初始化</font>**<font style="color:rgba(6, 8, 31, 0.88);">：如JDBC连接、第三方框架对象</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">动态代理对象创建</font>**<font style="color:rgba(6, 8, 31, 0.88);">：MyBatis中的Mapper接口代理对象就是通过FactoryBean创建的</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">条件化Bean创建</font>**<font style="color:rgba(6, 8, 31, 0.88);">：根据条件创建不同实现的Bean</font>

```java
// FactoryBean实现示例  
public class UserServiceFactoryBean implements FactoryBean<UserService> {  

    @Override  
    public UserService getObject() throws Exception {  
        // 复杂的创建逻辑  
        UserService service = new UserServiceImpl();  
        // 执行一系列初始化操作  
        return service;  
    }  

    @Override  
    public Class<?> getObjectType() {  
        return UserService.class;  
    }  

    @Override  
    public boolean isSingleton() {  
        return true;  
    }  
}
```

## <font style="color:rgba(6, 8, 31, 0.88);">四、关键区别详解</font>
### <font style="color:rgba(6, 8, 31, 0.88);">1. 角色不同</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">BeanFactory</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>**<font style="color:rgba(6, 8, 31, 0.88);">容器角色</font>**<font style="color:rgba(6, 8, 31, 0.88);">，管理所有Bean</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">FactoryBean</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>**<font style="color:rgba(6, 8, 31, 0.88);">工厂Bean角色</font>**<font style="color:rgba(6, 8, 31, 0.88);">，是容器中的一个Bean</font>

### <font style="color:rgba(6, 8, 31, 0.88);">2. 使用方式不同</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">BeanFactory</font>**<font style="color:rgba(6, 8, 31, 0.88);">：通常由Spring框架实现，开发者很少直接实现</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">FactoryBean</font>**<font style="color:rgba(6, 8, 31, 0.88);">：开发者实现该接口，自定义Bean的创建过程</font>

### <font style="color:rgba(6, 8, 31, 0.88);">3. 获取方式有特别之处</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">通过</font>`<font style="color:rgba(6, 8, 31, 0.88);">context.getBean("beanName")</font>`<font style="color:rgba(6, 8, 31, 0.88);">获取FactoryBean创建的对象</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">通过</font>`<font style="color:rgba(6, 8, 31, 0.88);">context.getBean("&beanName")</font>`<font style="color:rgba(6, 8, 31, 0.88);">获取FactoryBean本身</font>

### <font style="color:rgba(6, 8, 31, 0.88);">4. 应用场景不同</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">BeanFactory</font>**<font style="color:rgba(6, 8, 31, 0.88);">：作为容器的基础设施使用</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">FactoryBean</font>**<font style="color:rgba(6, 8, 31, 0.88);">：解决特定Bean的复杂创建问题</font>

## <font style="color:rgba(6, 8, 31, 0.88);">五、面试回答技巧</font>
<font style="color:rgba(6, 8, 31, 0.88);">回答这个问题时，建议采用以下结构：</font>

1. **<font style="color:rgba(6, 8, 31, 0.88);">先说明基本区别</font>**<font style="color:rgba(6, 8, 31, 0.88);">：一个是容器，一个是产生Bean的工厂Bean</font>
2. **<font style="color:rgba(6, 8, 31, 0.88);">分别解释各自职责</font>**<font style="color:rgba(6, 8, 31, 0.88);">：BeanFactory管理Bean，FactoryBean创建特定Bean</font>
3. **<font style="color:rgba(6, 8, 31, 0.88);">举例说明</font>**<font style="color:rgba(6, 8, 31, 0.88);">：MyBatis的Mapper接口是通过MapperFactoryBean创建的</font>
4. **<font style="color:rgba(6, 8, 31, 0.88);">补充实现细节</font>**<font style="color:rgba(6, 8, 31, 0.88);">：提到FactoryBean的三个核心方法及获取特殊性</font>
5. **<font style="color:rgba(6, 8, 31, 0.88);">强调应用场景</font>**<font style="color:rgba(6, 8, 31, 0.88);">：说明什么情况下会使用FactoryBean</font>

## <font style="color:rgba(6, 8, 31, 0.88);">六、经典例子</font>
**<font style="color:rgba(6, 8, 31, 0.88);">实际工作中的例子</font>**<font style="color:rgba(6, 8, 31, 0.88);">：Spring整合MyBatis时，我们只需定义Mapper接口，不需要实现类。这是因为MyBatis使用了MapperFactoryBean(一个FactoryBean实现)，它在运行时动态创建Mapper接口的代理实现。</font>

```java
// Spring整合MyBatis的配置  
@Bean  
public MapperFactoryBean<UserMapper> userMapper() {  
    MapperFactoryBean<UserMapper> factoryBean = new MapperFactoryBean<>();  
    factoryBean.setMapperInterface(UserMapper.class);  
    factoryBean.setSqlSessionFactory(sqlSessionFactory());  
    return factoryBean;  
}  

// 简化后的注解方式  
@MapperScan("com.example.mapper")
```

## <font style="color:rgba(6, 8, 31, 0.88);">总结</font>
<font style="color:rgba(6, 8, 31, 0.88);">面试中回答BeanFactory与FactoryBean区别时，关键是要明确：</font>

+ <font style="color:rgba(6, 8, 31, 0.88);">BeanFactory是</font>**<font style="color:rgba(6, 8, 31, 0.88);">容器</font>**<font style="color:rgba(6, 8, 31, 0.88);">，管理所有Bean</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">FactoryBean是特殊的</font>**<font style="color:rgba(6, 8, 31, 0.88);">Bean</font>**<font style="color:rgba(6, 8, 31, 0.88);">，用于创建复杂Bean</font>

<font style="color:rgba(6, 8, 31, 0.88);">通过这种方式回答，既点明了核心区别，又展示了技术深度，相信大家在面试中一定能够从容应对这个问题！</font>



> 更新: 2025-04-07 22:11:42  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dds43egxxx36x9dw>