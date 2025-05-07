# RestTemplate连接池怎么优化？

有同学私信我说面试被问RestTemplate连接池怎么优化， 直接问懵了，   

数据库连接池、线程池我知道， RestTemplate连接池是个啥？

RestTemplate是通过建立http连接进行远程访问的，其实http连接也是有池化概念的，不过RestTemplate默认没有使用连接池，而是每次远程访问都会创建一个新的http连接，那我们应该知道， 每次<font style="color:rgb(77, 77, 77);">http连接</font>都会进行<font style="color:rgb(77, 77, 77);">tcp的3次握手和4次挥手，  如果在高并发场景下，其实可以通过连接池的方式， 建立长连接， 这样省去了每次建立新的连接带来的性能消耗。 </font>



 可以直接针对RestTemplate配置ClientHttpRequestFactory为 okHttp或者httpClient， 他们都提供了现成的连接池实现。可以设置最大连接数， 空闲连接存活时长。  

当然

<font style="color:rgb(77, 77, 77);">1.http连接池不是万能的,过多的长连接会占用服务器资源,导致其他服务受阻</font>  
<font style="color:rgb(77, 77, 77);">2.http连接池只适用于请求是经常访问同一主机(或同一个接口)的情况下</font>  
<font style="color:rgb(77, 77, 77);">3.并发数不高的情况下资源利用率低下</font>

<font style="color:rgb(77, 77, 77);"></font>

<font style="color:rgb(77, 77, 77);">好如果视频对你有帮助可以三连支持， 需要RestTemplate连接池示例代码可以评论区扣666</font>







> 更新: 2024-06-28 13:37:15  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/nyz74xb13w2mxhgh>