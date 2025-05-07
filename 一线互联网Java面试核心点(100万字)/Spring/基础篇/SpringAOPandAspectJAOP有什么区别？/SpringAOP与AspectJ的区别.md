# Spring AOP 与 AspectJ 的区别

## <font style="color:rgba(6, 8, 31, 0.88);">Spring AOP的实现原理</font>
<font style="color:rgba(6, 8, 31, 0.88);">Spring AOP 基于</font>**<font style="color:rgba(6, 8, 31, 0.88);">代理模式</font>**<font style="color:rgba(6, 8, 31, 0.88);">实现，主要采用两种代理方式（Spring 框架采用了 AspectJ 的注解风格，但底层实现仍保持了 Spring AOP 自身的代理机制）： </font>

### <font style="color:rgba(6, 8, 31, 0.88);">1. 代理机制</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">JDK动态代理</font>**<font style="color:rgba(6, 8, 31, 0.88);">：当目标类实现了接口时使用，基于</font>`<font style="color:rgba(6, 8, 31, 0.88);">java.lang.reflect.Proxy</font>`
+ **<font style="color:rgba(6, 8, 31, 0.88);">CGLIB代理</font>**<font style="color:rgba(6, 8, 31, 0.88);">：当目标类没有实现接口时使用，通过继承目标类生成子类</font>

### <font style="color:rgba(6, 8, 31, 0.88);">2. 运行时织入流程</font>
```java
// 简化的Spring AOP代理创建过程  
public Object createProxy(Object target) {  
    // 创建代理工厂  
    ProxyFactory factory = new ProxyFactory();  
    factory.setTarget(target);  
    
    // 添加通知和顾问  
    factory.addAdvice(new AroundAdvice());  
    
    // 获取代理对象  
    return factory.getProxy();  
}
```

### <font style="color:rgba(6, 8, 31, 0.88);">3. 核心类</font>
+ `<font style="color:rgba(6, 8, 31, 0.88);">ProxyFactory</font>`<font style="color:rgba(6, 8, 31, 0.88);">：代理工厂，负责创建AOP代理</font>
+ `<font style="color:rgba(6, 8, 31, 0.88);">AopProxy</font>`<font style="color:rgba(6, 8, 31, 0.88);">：代理接口，有JDK和CGLIB两种实现</font>
+ `<font style="color:rgba(6, 8, 31, 0.88);">DefaultAopProxyFactory</font>`<font style="color:rgba(6, 8, 31, 0.88);">：根据条件选择代理实现</font>
+ `<font style="color:rgba(6, 8, 31, 0.88);">ReflectiveMethodInvocation</font>`<font style="color:rgba(6, 8, 31, 0.88);">：方法调用链的实现</font>

### <font style="color:rgba(6, 8, 31, 0.88);">4. 代理调用过程</font>
1. <font style="color:rgba(6, 8, 31, 0.88);">客户端调用代理对象的方法</font>
2. <font style="color:rgba(6, 8, 31, 0.88);">代理对象将调用委托给拦截器链</font>
3. <font style="color:rgba(6, 8, 31, 0.88);">拦截器链按顺序执行所有通知</font>
4. <font style="color:rgba(6, 8, 31, 0.88);">最终调用目标对象的方法</font>

## <font style="color:rgba(6, 8, 31, 0.88);">AspectJ的实现原理</font>
<font style="color:rgba(6, 8, 31, 0.88);">AspectJ是完整的AOP解决方案，基于</font>**<font style="color:rgba(6, 8, 31, 0.88);">字节码修改</font>**<font style="color:rgba(6, 8, 31, 0.88);">技术实现：</font>

### <font style="color:rgba(6, 8, 31, 0.88);">1. 织入方式</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">编译时织入</font>**<font style="color:rgba(6, 8, 31, 0.88);">：使用ajc编译器在编译源码时织入</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">后编译织入</font>**<font style="color:rgba(6, 8, 31, 0.88);">：对已编译的.class文件进行转换</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">加载时织入(LTW)</font>**<font style="color:rgba(6, 8, 31, 0.88);">：在JVM加载类时动态修改字节码</font>

### <font style="color:rgba(6, 8, 31, 0.88);">2. 实现技术</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">使用专门的AspectJ编译器(ajc)</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">直接修改字节码，将切面逻辑编织到目标类中</font>
+ <font style="color:rgba(6, 8, 31, 0.88);">不依赖运行时的代理机制</font>

### <font style="color:rgba(6, 8, 31, 0.88);">3. 织入示例</font>
```java
// 使用AspectJ的LTW配置  
public static void main(String[] args) {  
    // 设置LTW代理  
    System.setProperty("java.agent", "path-to-aspectjweaver.jar");  

    // 正常运行应用，AspectJ会在类加载时织入  
    Application.main(args);  
}
```

## <font style="color:rgba(6, 8, 31, 0.88);">Spring AOP与AspectJ对比</font>
| **<font style="color:rgba(6, 8, 31, 0.88);">特性</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">Spring AOP</font>** | **<font style="color:rgba(6, 8, 31, 0.88);">AspectJ</font>** |
| --- | --- | --- |
| **<font style="color:rgba(6, 8, 31, 0.88);">实现方式</font>** | <font style="color:rgba(6, 8, 31, 0.88);">动态代理（运行时）</font> | <font style="color:rgba(6, 8, 31, 0.88);">字节码修改（编译时/加载时）</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">织入时机</font>** | <font style="color:rgba(6, 8, 31, 0.88);">运行时</font> | <font style="color:rgba(6, 8, 31, 0.88);">编译时、后编译时、加载时</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">性能</font>** | <font style="color:rgba(6, 8, 31, 0.88);">相对较低（有代理开销）</font> | <font style="color:rgba(6, 8, 31, 0.88);">较高（无代理，直接修改字节码）</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">功能范围</font>** | <font style="color:rgba(6, 8, 31, 0.88);">仅支持方法级别切点</font> | <font style="color:rgba(6, 8, 31, 0.88);">全面支持（方法、字段、构造函数等）</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">连接点类型</font>** | <font style="color:rgba(6, 8, 31, 0.88);">仅方法执行</font> | <font style="color:rgba(6, 8, 31, 0.88);">方法调用、字段访问、异常处理等多种</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">代理限制</font>** | <font style="color:rgba(6, 8, 31, 0.88);">需创建代理对象，有self-invocation问题</font> | <font style="color:rgba(6, 8, 31, 0.88);">无代理限制，无self-invocation问题</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">与Spring集成</font>** | <font style="color:rgba(6, 8, 31, 0.88);">原生集成，配置简单</font> | <font style="color:rgba(6, 8, 31, 0.88);">需额外配置，但Spring提供了集成支持</font> |
| **<font style="color:rgba(6, 8, 31, 0.88);">使用复杂度</font>** | <font style="color:rgba(6, 8, 31, 0.88);">简单易用</font> | <font style="color:rgba(6, 8, 31, 0.88);">功能强大但学习曲线陡峭</font> |


## <font style="color:rgba(6, 8, 31, 0.88);">Spring中使用AspectJ语法</font>
<font style="color:rgba(6, 8, 31, 0.88);">Spring提供了对AspectJ注解风格的支持，但底层仍使用Spring AOP机制：</font>

```java
// Spring中使用AspectJ风格注解定义切面  
@Aspect  
@Component  
public class LoggingAspect {  
    @Before("execution(* com.example.service.*.*(..))")  
    public void logBefore(JoinPoint joinPoint) {  
        // 前置通知逻辑  
    }  
}  

// 启用AspectJ注解支持  
@Configuration  
@EnableAspectJAutoProxy  
public class AppConfig {  
    // 配置  
}
```

## <font style="color:rgba(6, 8, 31, 0.88);">选择建议</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">选择Spring AOP</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">当仅需要简单的方法拦截</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">需要与Spring无缝集成</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">对性能要求不是极端苛刻</font>
+ **<font style="color:rgba(6, 8, 31, 0.88);">选择AspectJ</font>**<font style="color:rgba(6, 8, 31, 0.88);">：</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">需要字段级别或构造函数级别的切点</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">需要拦截不由Spring管理的对象</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">有self-invocation（目标对象内部方法调用）需求</font>
    - <font style="color:rgba(6, 8, 31, 0.88);">对性能有极高要求</font>

<font style="color:rgba(6, 8, 31, 0.88);">Spring AOP满足了大多数企业应用的AOP需求，而AspectJ则提供了更强大但复杂度更高的AOP能力。两者各有优势，可根据实际需求选择。</font>



> 更新: 2025-04-14 20:30:26  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hthr1488kgh8xhgz>