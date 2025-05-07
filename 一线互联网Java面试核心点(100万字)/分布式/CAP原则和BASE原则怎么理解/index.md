# CAP原则和BASE原则怎么理解

<font style="color:rgba(0, 0, 0, 0.82);">CAP原则和BASE原则都是在分布式系统设计中常用的概念。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">CAP原则</font>
<font style="color:rgba(0, 0, 0, 0.82);">CAP原则是由Eric Brewer提出的分布式系统设计的基本定理。它指出在一个分布式系统中，以下三个特性最多只能同时满足其中两个：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">Consistency（一致性）：所有节点在同一时间具有相同的数据。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Availability（可用性）：保证每个请求都会收到一个响应，无论响应成功或失败。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Partition Tolerance（分区容错性）：分区容错性表明系统能够容忍网络中的任意分区或节点失效。当网络节点之间无法通信时，系统仍然必须正常运作。</font>

<font style="color:rgba(0, 0, 0, 0.82);">在实际应用中，由于网络分区是不可避免的，所以在CAP中通常只能在C和A之间做出选择。</font>

**<font style="color:rgba(0, 0, 0, 0.82);"></font>**

**<font style="color:rgba(0, 0, 0, 0.82);">为什么CAP原则最多只能同时满足其中两个？</font>**

<font style="color:rgba(0, 0, 0, 0.82);">假设有一个分布式数据库，分布在两个数据中心A和B。如果A和B之间的网络连接断开：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">如果我们选择保证一致性（C）和分区容错性（P），那么我们必须让至少一个数据中心停止接受写操作，以避免数据不一致，这就牺牲了可用性（A）。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">如果我们选择保证可用性（A）和分区容错性（P），那么两个数据中心都可以继续独立工作，但可能会导致数据不一致，因此牺牲了一致性（C）。</font>



### <font style="color:rgba(0, 0, 0, 0.82);">BASE原则</font>
<font style="color:rgba(0, 0, 0, 0.82);">BASE是对CAP中一致性和可用性权衡的结果，它的全称是：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">Basically Available（基本可用）</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Soft state（软状态）</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">Eventually consistent（最终一致性）</font>

<font style="color:rgba(0, 0, 0, 0.82);">BASE原则是对CAP中AP的一个延伸，它的主要思想是：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">基本可用：系统在出现故障时，保证核心可用，允许损失部分可用性。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">软状态：允许系统中的数据存在中间状态，并认为该状态不会影响系统整体可用性。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">最终一致性：系统中所有的数据副本，在经过一段时间后，最终能够达到一致的状态。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">举一个符合BASE原则场景例子：</font>**

<font style="color:rgba(0, 0, 0, 0.82);">在一个大型社交媒体平台上，用户可以在线更新他们的个人状态（例如，发布心情、描述活动等）。该平台有多个数据中心，分布在不同的地理位置，以支持全球用户的低延迟访问。为了能够快速响应用户请求并保持高可用性，该平台选择遵循BASE原则。</font>

**<font style="color:rgba(0, 0, 0, 0.82);">符合BASE原则的特征：</font>**

1. **<font style="color:rgba(0, 0, 0, 0.82);">基本可用（Basically Available）：</font>**
    - <font style="color:rgba(0, 0, 0, 0.82);">在这个系统中，即使有部分数据中心出现故障，其他数据中心依然可以处理用户的状态更新和查看请求。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">用户可以不间断地继续发布状态，而不需要等待所有数据中心同步完成。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">软状态（Soft state）：</font>**
    - <font style="color:rgba(0, 0, 0, 0.82);">用户发布的状态信息在传播过程中，允许在短时间内不同数据中心的数据有所不同。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">不一致被认为是暂时的，并且在最终一致性（eventual consistency）下会得到解决。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">最终一致性（Eventually Consistent）：</font>**
    - <font style="color:rgba(0, 0, 0, 0.82);">虽然在某个时间点，不同的数据中心可能会显示出不同的用户状态，但是随着时间的推移，通过后台的同步和合并机制，所有数据中心最终会达到一致的状态。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">系统可能使用异步复制来慢慢将所有数据中心的数据同步一致。</font>

<font style="color:rgba(0, 0, 0, 0.82);">BASE原则是对CAP中一致性和可用性权衡的结果，它通过牺牲强一致性来获得可用性，并允许数据在一段时间内是不一致的，但最终达到一致状态。</font>

<font style="color:rgba(0, 0, 0, 0.82);">在Java分布式系统开发中，我们经常需要根据具体业务需求来选择适合的原则。例如：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">对于需要强一致性的场景（如银行交易），可能更倾向于选择CP（一致性和分区容错性）。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">对于可以容忍短期不一致，但需要高可用的场景（如社交网络的点赞功能），可能更适合选择AP（可用性和分区容错性）并遵循BASE原则。</font>

<font style="color:rgba(0, 0, 0, 0.82);">在实际应用中，我们可能会使用各种技术和框架来实现这些原则，如分布式事务、最终一致性等。理解这些原则对于设计可靠的分布式系统至关重要。</font>



> 更新: 2024-08-21 21:35:26  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/yy6maco9ampyrei3>