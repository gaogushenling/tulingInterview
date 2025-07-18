# SpringBoot的启动原理？

1. **运行Main方法：**<font style="color:rgb(55, 65, 81);"> 应用程序启动始于Main方法的执行。在Main方法中，创建了一个SpringApplication实例，用于引导应用程序的启动。同时，SpringApplication会根据</font>**spring.factories**<font style="color:rgb(55, 65, 81);">文件加载并注册监听器、ApplicationContextInitializer等扩展接口实现。</font>
2. **运行run方法：**<font style="color:rgb(55, 65, 81);"> 运行SpringApplication的</font>**run**<font style="color:rgb(55, 65, 81);">方法是应用程序启动的入口。在这一步，Spring Boot会 启动Spring进而创建内置tomcat，进去run方法后还做了很多其他事：</font>
3. ****<font style="color:rgb(55, 65, 81);"> Spring Boot会读取和解析环境变量、配置文件（如</font>**application.properties**<font style="color:rgb(55, 65, 81);">或</font>**application.yml**<font style="color:rgb(55, 65, 81);">）等，以获取应用程序的配置信息。</font>
4. **之后再创建ApplicationContext也就是我们熟知的Spring上下文：**<font style="color:rgb(55, 65, 81);"> 在这一步，Spring Boot会根据应用程序的类型（例如，Web应用程序）创建相应的ApplicationContext。对于Web应用程序，通常创建的是</font>**ServletWebServerApplicationContext**<font style="color:rgb(55, 65, 81);">。</font>
5. **预初始化上下文：**<font style="color:rgb(55, 65, 81);"> Spring Boot会将启动类作为配置类，读取并注册为BeanDefinition，这使得Spring容器可以识别应用程序的配置。</font>
6. **调用refresh：**<font style="color:rgb(55, 65, 81);"> 此时，Spring Boot调用了</font>**refresh**<font style="color:rgb(55, 65, 81);">方法来加载和初始化Spring容器。在这一过程中，会执行一系列操作，包括解析</font>**@Import**<font style="color:rgb(55, 65, 81);">注解以加载自动配置类，创建和注册BeanDefinition等。</font>
7. **创建内置servlet容器：**<font style="color:rgb(55, 65, 81);"> 如果应用程序是一个Web应用程序，Spring Boot会在这一步创建内置的servlet容器（例如Tomcat），以便应用程序可以接受HTTP请求。这个容器将被Spring Boot自动配置，并且可以通过配置进行自定义。</font>
8. **监听器和扩展点：**<font style="color:rgb(55, 65, 81);"> 在整个启动过程中，Spring Boot会调用各种监听器和扩展点，这些组件可以用来对应用程序进行扩展和定制。例如，您可以使用监听器来处理应用程序启动和关闭事件，或者使用ApplicationContextInitializer来自定义ApplicationContext的初始化。</font>

<font style="color:rgb(55, 65, 81);">总的来说，Spring Boot的启动过程是一个复杂的流程，它从Main方法开始，经过一系列步骤来初始化Spring容器和启动内置tomcat。</font>



> 更新: 2023-09-08 11:42:14  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/xspr2fqxkyo63s1l>