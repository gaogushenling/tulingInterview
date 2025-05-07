# Spring-Ioc容器的加载过程

<font style="color:rgb(44, 62, 80);">Spring 的 IOC 容器工作的过程，其实可以划分为两个阶段：</font>**<font style="color:rgb(44, 62, 80);">配置解析阶段</font>**<font style="color:rgb(44, 62, 80);">和</font>**<font style="color:rgb(44, 62, 80);">Bean 的创建段</font>**<font style="color:rgb(44, 62, 80);">。</font>

<font style="color:rgb(44, 62, 80);">其中</font>

+ **<font style="color:rgb(44, 62, 80);">配置解析阶段</font>**<font style="color:rgb(44, 62, 80);">主要做的工作是加载和解析配置文件，将配置的bean解析成 BeanDefinition。</font>
    - <font style="color:rgb(44, 62, 80);">整个过程是： </font>
1. <font style="color:rgb(44, 62, 80);">读取配置：通过BeanDefinitionReader读取配置文件或配置类</font>
2. <font style="color:rgb(44, 62, 80);">解析配置信息：如ComonentScan、Bean配置等</font>
3. <font style="color:rgb(44, 62, 80);">扫描类注解：根据ComonentScan扫描@Component、@Bean、@Configuration、@Import等注解... </font>
4. <font style="color:rgb(44, 62, 80);">将符合的bean注册为BeanDefinition</font>
+ **<font style="color:rgb(44, 62, 80);">Bean的创建过程主要做的工作是根据 BeanDefinition创建Bean。</font>**
    - <font style="color:rgb(44, 62, 80);">大概过程：</font>
1. <font style="color:rgb(36, 41, 47);">实例化Bean：容器根据配置文件中的Bean定义，实例化Bean对象。可以通过构造函数实例化、工厂方法实例化、静态工厂方法实例化等方式来创建Bean对象。</font>
2. <font style="color:rgb(36, 41, 47);">注入Bean属性：容器会为实例化的Bean对象设置属性值，可以通过setter方法注入属性值，也可以通过构造函数注入属性值。</font>
3. <font style="color:rgb(36, 41, 47);">处理依赖关系：容器会处理Bean之间的依赖关系，将依赖的Bean注入到需要的地方 。</font>
4. <font style="color:rgb(36, 41, 47);">执行初始化方法：容器会调用Bean的初始化方法，可以通过实现InitializingBean接口或在配置文件中指定初始化方法来定义Bean的初始化逻辑。</font>
5. <font style="color:rgb(36, 41, 47);">注册Bean：容器会将实例化、属性设置和初始化完成的Bean对象注册到容器中，以便后续的使用和管理。</font>
6. <font style="color:rgb(36, 41, 47);">完成加载：容器完成所有Bean的加载和初始化后，即完成了IoC容器的加载过程。此时，可以通过容器调用getBean获取Bean对象。</font>



> 更新: 2023-09-06 20:10:22  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/mngu0f2geg1u2e53>