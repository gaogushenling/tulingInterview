# BeanFactory 和 ApplicationContext有什么区别？

<font style="color:rgb(77, 77, 77);">BeanFactory和ApplicationContext是Spring的两大核心接口，</font>**<font style="color:rgb(77, 77, 77);">都可以当做Spring的容器</font>**<font style="color:rgb(77, 77, 77);">。其中ApplicationContext是BeanFactory的子接口。</font>

<font style="color:rgb(77, 77, 77);"></font>

<font style="color:rgb(77, 77, 77);"> </font>

<font style="color:rgb(77, 77, 77);">BeanFactory：</font><font style="color:rgb(36, 41, 47);">是Spring框架的核心接口之一， </font><font style="color:rgb(77, 77, 77);">我们可以称之为 </font>**<font style="color:rgb(77, 77, 77);">“低级容器”</font>**<font style="color:rgb(77, 77, 77);">。</font><font style="color:rgb(51, 51, 51);">为什么叫低级容器呢？因为Bean的生产过程分为【配置的解析】和【Bean的创建】，而</font><font style="color:rgb(77, 77, 77);">BeanFactory只有</font><font style="color:rgb(51, 51, 51);">Bean的创建功能，但也说明它内存占用更小，在早期会在一些内存受限的可穿戴设备中作为spring容器使用。</font>

<font style="color:rgb(77, 77, 77);"></font>

<font style="color:rgb(77, 77, 77);">ApplicationContext 可以称之为 </font>**<font style="color:rgb(77, 77, 77);">“高级容器”</font>**<font style="color:rgb(77, 77, 77);">。因为他比 BeanFactory 多了更多的功能。他继承了多个接口，因此具备了更多的功能。例如配置的读取、解析、扫描等，还加入了 如 事件事件监听机制，及后置处理器让Spring提升了扩展性。所以你看他的名字，已经不是 BeanFactory 之类的工厂了，而是 “应用上下文”， 代表着整个大容器的所有功能。该接口定义了一个 refresh 方法，此方法是所有阅读 Spring 源码的人的最熟悉的方法，用于刷新整个容器，即重新加载/刷新所有的 bean。</font>

<font style="color:rgb(77, 77, 77);"></font>

 



> 更新: 2023-09-06 21:07:32  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/cce5mfzh2uip4r4v>