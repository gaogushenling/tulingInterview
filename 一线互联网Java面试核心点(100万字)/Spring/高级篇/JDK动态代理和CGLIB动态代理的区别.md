# JDK动态代理和CGLIB动态代理的区别

**<font style="background-color:rgb(247, 247, 248);">从性能上特性对比：</font>**

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">JDK动态代理要求目标对象必须实现至少一个接口，因为它基于接口生成代理类。而CGLIB动态代理不依赖于目标对象是否实现接口，可以代理没有实现接口的类，它通过继承或者代理目标对象的父类来实现代理。</font>

**<font style="background-color:rgb(247, 247, 248);">从创建代理时的性能对比：</font>**

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">JDK动态代理通常比CGLIB动态代理创建速度更快，因为它不需要生成字节码文件。而CGLIB动态代理的创建速度通常比较慢，因为它需要生成字节码文件。另外，JDK代理生成的代理类较小，占用较少的内存，而CGLIB生成的代理类通常较大，占用更多的内存。</font>

**<font style="background-color:rgb(247, 247, 248);">从调用时的性能对比：</font>**

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">JDK动态代理在方法调用时需要通过反射机制来调用目标方法，因此性能略低于CGLIB，尽管JDK动态代理在Java 8中有了性能改进，但CGLIB动态代理仍然具有更高的方法调用性能。CGLIB动态代理在方法调用时不需要通过反射，直接调用目标方法，通常具有更高的方法调用性能，同时无需类型转换。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">选择使用JDK动态代理还是CGLIB动态代理取决于具体需求。如果目标对象已经实现了接口，并且您更关注创建性能和内存占用，那么JDK动态代理可能是一个不错的选择。如果目标对象没有实现接口，或者您更关注方法调用性能，那么CGLIB动态代理可能更合适。综上所述，这两种代理方式各有优势，根据实际情况进行选择是明智的，  Spring默认情况如果目标类实现了接口用JDK代理否则用CGLIB。  而SpringBoot默认用CGLIB，所以用哪个问题都不大。</font>



> 更新: 2023-09-17 15:07:33  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/grivzt20iibg114q>