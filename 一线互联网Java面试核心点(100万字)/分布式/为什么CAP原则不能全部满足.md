# 为什么CAP原则不能全部满足

## <font style="color:rgb(79, 79, 79);">(1) 场景</font>
如下图，是我们证明CAP的基本场景，分布式网络中有两个节点Host1和Host2，他们之间网络可以连通，Host1中运行Process1程序和对应的数据库Data，Host2中运行Process2程序和对应数据库Data。

![1695886396622-ac6110b1-8bb1-4f0f-ac7b-d96792d3bbfc.png](./img/v0wp_ou0gNDicxu3/1695886396622-ac6110b1-8bb1-4f0f-ac7b-d96792d3bbfc-162793.png)



## (2) CAP特性
+ <font style="background-color:rgba(255,244,245,1);">如果满足一致性(C)</font>：那么Data(0) = Data(0).
+ <font style="background-color:rgba(255,244,245,1);">如果满足可用性(A)</font>: 用户不管请求Host1或Host2，都会立刻响应结果。
+ <font style="background-color:rgba(255,244,245,1);">如果满足分区容错性(P)</font>: Host1或Host2有一方脱离系统(故障)， 都不会影响Host1和Host2彼此之间正常运作。



## (3) 分布式系统正常运行流程
如下图，是分布式系统正常运转的流程。

> <font style="color:rgb(85, 86, 102);">A、用户向</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">主机请求数据更新，程序</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Process1</font><font style="color:rgb(85, 86, 102);">更新数据库</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(0)</font><font style="color:rgb(85, 86, 102);">为</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(1)</font>
>
> <font style="color:rgb(85, 86, 102);">B、分布式系统将数据进行同步操作，将</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">中的</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(1)</font><font style="color:rgb(85, 86, 102);">同步的</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">中``Data(0)</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">,使</font><font style="color:rgb(85, 86, 102);">Host2</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">中的数据也变为</font><font style="color:rgb(85, 86, 102);">Data(1)`</font>
>
> <font style="color:rgb(85, 86, 102);">C、当用户请求主机</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">时，则</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Process2</font><font style="color:rgb(85, 86, 102);">则响应最新的</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(1)</font><font style="color:rgb(85, 86, 102);">数据</font>
>



![1695886629504-a0f483cb-9ae2-4214-ac88-05f7672311fb.png](./img/v0wp_ou0gNDicxu3/1695886629504-a0f483cb-9ae2-4214-ac88-05f7672311fb-214502.png)



根据CAP的特性：

> <font style="color:rgb(85, 86, 102);">1.</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">的数据库</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data</font><font style="color:rgb(85, 86, 102);">之间的数据是否一样为一致性</font><font style="color:rgb(85, 86, 102);">©</font>
>
> <font style="color:rgb(85, 86, 102);">2.用户对</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">的请求响应为可用性(A)</font>
>
> <font style="color:rgb(85, 86, 102);">3.</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">之间的各自网络环境为分区容错性§</font>
>



当前是一个正常运作的流程，目前CAP三个特性可以同时满足，也是一个<font style="color:#DF2A3F;background-color:rgba(255,244,245,1);">理想状态</font>,但是实际应用场景中，发生错误在所难免，那么如果发生错误CAP是否能同时满足，或者该如何取舍？



## (4) 分布式系统异常运行流程
<font style="color:rgb(85, 86, 102);background-color:rgb(238, 240, 244);">假设</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);background-color:rgb(238, 240, 244);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);background-color:rgb(238, 240, 244);">之间的网络断开了，我们要支持这种网络异常，相当于要满足</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">分区容错性(P)</font><font style="color:rgb(85, 86, 102);background-color:rgb(238, 240, 244);">，能不能同时满足</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">一致性(C)</font><font style="color:rgb(85, 86, 102);background-color:rgb(238, 240, 244);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">可用响应性(A)</font><font style="color:rgb(85, 86, 102);background-color:rgb(238, 240, 244);">呢？</font>



![1695886761084-76c54fbf-18b9-4998-bec5-56c6424dcf86.png](./img/v0wp_ou0gNDicxu3/1695886761084-76c54fbf-18b9-4998-bec5-56c6424dcf86-742411.png)



假设在N1和N2之间网络断开的时候，

> <font style="color:rgb(85, 86, 102);">A、用户向</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">发送数据更新请求，那</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">中的数据</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(0)</font><font style="color:rgb(85, 86, 102);">将被更新为</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(1)</font>
>
> <font style="color:rgb(85, 86, 102);">B、弱此时</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host1</font><font style="color:rgb(85, 86, 102);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">网络是断开的，所以分布式系统同步操作将失败，</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">中的数据依旧是</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(0)</font>
>
> <font style="color:rgb(85, 86, 102);">C、有用户向</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Host2</font><font style="color:rgb(85, 86, 102);">发送数据读取请求，由于数据还没有进行同步，</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Process2</font><font style="color:rgb(85, 86, 102);">没办法立即给用户返回最新的数据V1，那么将面临两个选择。</font>
>
> <font style="color:rgb(85, 86, 102);">第一，牺牲</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">数据一致性(c)</font><font style="color:rgb(85, 86, 102);">，响应旧的数据</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(0)</font><font style="color:rgb(85, 86, 102);">给用户；</font>
>
> <font style="color:rgb(85, 86, 102);">第二，牺牲</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">可用性(A)</font><font style="color:rgb(85, 86, 102);">，阻塞等待，直到网络连接恢复，数据同步完成之后，再给用户响应最新的数据</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">Data(1)</font><font style="color:rgb(85, 86, 102);">。</font>
>
> <font style="color:rgb(85, 86, 102);">这个过程，证明了要满足</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">分区容错性(p)</font><font style="color:rgb(85, 86, 102);">的分布式系统，只能在</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">一致性(C)</font><font style="color:rgb(85, 86, 102);">和</font><font style="color:rgb(199, 37, 78);background-color:rgb(249, 242, 244);">可用性(A)</font><font style="color:rgb(85, 86, 102);">两者中，选择其中一个。</font>
>



## (5) "3选2"的必然性
> <font style="color:rgb(85, 86, 102);">通过CAP理论，我们知道无法同时满足</font><font style="color:rgb(199, 37, 78);">一致性</font><font style="color:rgb(85, 86, 102);">、</font><font style="color:rgb(199, 37, 78);">可用性</font><font style="color:rgb(85, 86, 102);">和</font><font style="color:rgb(199, 37, 78);">分区容错性</font><font style="color:rgb(85, 86, 102);">这三个特性，那要舍弃哪个呢？</font>
>



### CA 放弃 P
<font style="color:rgb(85, 86, 102);">一个分布式系统中，不可能存在不满足P，放弃</font><font style="color:rgb(199, 37, 78);">分区容错性(p)</font><font style="color:rgb(85, 86, 102);">，即不进行分区，不考虑由于网络不通或结点挂掉的问题，则可以实现一致性和可用性。那么系统将不是一个标准的分布式系统。我们最常用的关系型数据就满足了CA，如下：</font>

![1695886910435-72d5d22e-85ee-4dee-86d1-c95325f66206.png](./img/v0wp_ou0gNDicxu3/1695886910435-72d5d22e-85ee-4dee-86d1-c95325f66206-684548.png)

主数据库和从数据库中间不再进行数据同步，数据库可以响应每次的查询请求，通过事务(原子性操作)隔离级别实现每个查询请求都可以返回最新的数据。



> 注意：
>
> 对于一个分布式系统来说。P是一个基本要求，CAP三者中，只能在CA两者之间做权衡，并且要想尽办法提升P。
>



### CP 放弃 A
如果一个分布式系统不要求强的可用性，即容许系统停机或者长时间无响应的话，就可以在CAP三者中保障CP而舍弃A。



放弃可用性，追求一致性和分区容错性，如Redis、HBase等，还有分布式系统中常用的Zookeeper也是在CAP三者之中选择优先保证CP的。



场景：



跨行转账，一次转账请求要等待双方银行系统都完成整个事务才算完成。



### AP 放弃 C
放弃一致性，追求分区容忍性和可用性。这是很多分布式系统设计时的选择。实现AP，前提是只要用户可以接受所查询的到数据在一定时间内不是最新的即可。



通常实现AP都会保证最终一致性，后面讲的BASE理论就是根据AP来扩展的。



场景1：

淘宝订单退款。今日退款成功，明日账户到账，只要用户可以接受在一定时间内到账即可。



场景2：

12306的买票。都是在可用性和一致性之间舍弃了一致性而选择可用性。

你在12306买票的时候肯定遇到过这种场景，当你购买的时候提示你是有票的（但是可能实际已经没票了），你也正常的去输入验证码，下单了。但是过了一会系统提示你下单失败，余票不足。这其实就是先在可用性方面保证系统可以正常的服务，然后在数据的一致性方面做了些牺牲，会影响一些用户体验，但是也不至于造成用户流程的严重阻塞。

但是，我们说很多网站牺牲了一致性，选择了可用性，这其实也不准确的。就比如上面的买票的例子，其实舍弃的只是强一致性。退而求其次保证了最终一致性。也就是说，虽然下单的瞬间，关于车票的库存可能存在数据不一致的情况，但是过了一段时间，还是要保证最终一致性的。



## (6) 总结:
**<font style="color:#DF2A3F;background-color:rgba(255,244,245,1);">CA 放弃 P</font>**：如果不要求P（不允许分区），则C（强一致性）和A（可用性）是可以保证的。这样分区将永远不会存在，因此CA的系统更多的是允许分区后各子系统依然保持CA。



**<font style="color:#DF2A3F;background-color:rgba(255,244,245,1);">CP 放弃 A</font>**：如果不要求A（可用），相当于每个请求都需要在Server之间强一致，而P（分区）会导致同步时间无限延长，如此CP也是可以保证的。很多传统的数据库分布式事务都属于这种模式。



**<font style="color:#DF2A3F;background-color:rgba(255,244,245,1);">AP 放弃 C</font>**：要高可用并允许分区，则需放弃一致性。一旦分区发生，节点之间可能会失去联系，为了高可用，每个节点只能用本地数据提供服务，而这样会导致全局数据的不一致性。现在众多的NoSQL都属于此类。

 



## 思考：
按照CAP理论如何设计一个电商系统？

首先个电商网站核心模块有用户，订单，商品，支付，促销管理等



1、对于用户模块，包括登录，个人设置，个人订单，购物车，收藏夹等，这些模块保证AP，数据短时间不一致不影响使用。

2、订单模块的下单付款扣减库存操作是整个系统的核心，CA都需要保证，极端情况下面牺牲A保证C

3、商品模块的商品上下架和库存管理保证CP

4、搜索功能因为本身就不是实时性非常高的模块，所以保证AP就可以了。

5、促销是短时间的数据不一致，结果就是优惠信息看不到，但是已有的优惠要保证可用，而且优惠可以提前预计算，所以可以保证AP。

6、支付这一块是独立的系统，或者使用第三方的支付宝，微信。其实CAP是由第三方来保证的，支付系统是一个对CAP要求极高的系统，C是必须要保证的，AP中A相对更重要，不能因为分区，导致所有人都不能支付

 



> 更新: 2023-09-28 15:46:38  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/hoqweg9el3zbuhpv>