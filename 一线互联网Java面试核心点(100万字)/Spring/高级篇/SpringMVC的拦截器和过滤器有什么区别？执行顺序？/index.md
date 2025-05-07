# SpringMVC的拦截器和过滤器有什么区别？执行顺序？

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">拦截器和过滤器在Web应用中都扮演着请求和响应处理的角色，但它们之间存在一些关键区别。</font>

**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">首先</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，</font>**<font style="color:#DF2A3F;background-color:rgb(247, 247, 248);">归属不同</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">。</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">拦截器是SpringMVC框架的一部分</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">而过滤器是Servlet规范的一部分</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">。拦截器主要用于对控制器层的请求进行处理，它们提供了更细粒度的控制，可以在请求进入控制器之前和之后执行特定的逻辑，例如身份验证、日志记录和权限检查。过滤器独立于SpringMVC，用于处理通用的请求和响应内容，例如字符编码、压缩和安全性。</font>

**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">其次</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，</font>**<font style="color:#DF2A3F;background-color:rgb(247, 247, 248);">执行顺序也不同</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">。拦截器的执行顺序由配置文件中的顺序决定，可以有多个拦截器，它们按照配置的顺序依次执行。而过滤器的执行顺序由</font>**<font style="background-color:rgb(247, 247, 248);">web.xml</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">文件中的配置顺序决定，同样可以有多个过滤器，按照配置的顺序执行。一般来说，首先执行过滤器，然后再执行拦截器。</font>

**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">最后</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">，</font>**<font style="color:#DF2A3F;background-color:rgb(247, 247, 248);">用途不同</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">。拦截器用于对SpringMVC的请求和响应进行特定的业务处理，通常与控制器层的请求处理有关。过滤器用于对所有Servlet请求和响应进行通用性的处理，通常关注请求和响应内容，而不涉及具体的业务逻辑。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">总的来说，了解拦截器和过滤器之间的这些区别非常重要。在面试中，这种理解将有助于说明您在Web应用程序中如何处理请求和响应以及如何利用SpringMVC和Servlet规范的不同功能。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"></font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"></font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">视频：</font>[<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">https://www.bilibili.com/video/BV1mf4y1c7cV?p=72&vd_source=fa810d8b8d6765676cb343ada918d6eb</font>](https://www.bilibili.com/video/BV1mf4y1c7cV?p=72&vd_source=fa810d8b8d6765676cb343ada918d6eb)



> 更新: 2024-03-08 10:40:53  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ls1c6qxmrgedfwvf>