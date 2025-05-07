# 解释Spring中bean的生命周期

Bean生命周期：指定的就是Bean从创建到销毁的整个过程: 分4大步：

1. <font style="color:rgb(0, 0, 0);">实例化</font>
    1. <font style="color:rgb(0, 0, 0);">通过反射去推断构造函数进行实例化</font>
    2. <font style="color:rgb(0, 0, 0);">实例工厂、 静态工厂</font>
2. <font style="color:rgb(0, 0, 0);">依赖注入（DI）</font>
    1. <font style="color:rgb(0, 0, 0);">解析自动装配（byname bytype constractor none @Autowired）</font>
3. <font style="color:rgb(0, 0, 0);">初始化</font>
    1. <font style="color:rgb(0, 0, 0);">调用很多Aware回调方法</font>
    2. <font style="color:rgb(0, 0, 0);">调用BeanPostProcessor.postProcessBeforeInitialization</font>
    3. <font style="color:rgb(0, 0, 0);">调用生命周期回调初始化方法</font>
    4. <font style="color:rgb(0, 0, 0);">调用BeanPostProcessor.postProcessAfterInitialization, 如果bean实现aop则会在这里创建动态代理</font>
4. <font style="color:rgb(0, 0, 0);">销毁</font>
    1. <font style="color:rgb(0, 0, 0);">在spring容器关闭的时候进行调用</font>
    2. <font style="color:rgb(0, 0, 0);">调用生命周期回调销毁方法</font>

<font style="color:rgb(0, 0, 0);"></font>

<font style="color:rgb(0, 0, 0);"></font>

<font style="color:rgb(0, 0, 0);">这4大步组成了Bean的生命周期</font>

<font style="color:rgb(0, 0, 0);"></font>



> 更新: 2023-09-06 21:39:04  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xp7igarg3iivvs54>