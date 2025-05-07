# 说下paxos算法

<font style="color:rgb(40, 202, 113);">Paxos</font><font style="color:rgb(74, 74, 74);"> 有点类似前面说的 </font><font style="color:rgb(40, 202, 113);">2PC</font><font style="color:rgb(74, 74, 74);">，</font><font style="color:rgb(40, 202, 113);">3PC</font><font style="color:rgb(74, 74, 74);">，但比这两种算法更加完善。在很多多大厂都得到了工程实践，比如阿里的 </font><font style="color:rgb(40, 202, 113);">OceanBase</font><font style="color:rgb(74, 74, 74);"> 的 </font>**<font style="color:rgb(74, 74, 74);">分布式数据库</font>**<font style="color:rgb(74, 74, 74);">， </font><font style="color:rgb(40, 202, 113);">Google</font><font style="color:rgb(74, 74, 74);"> 的 </font><font style="color:rgb(40, 202, 113);">chubby</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">分布式锁</font>**<font style="color:rgb(74, 74, 74);"> 。</font>

### **<font style="color:rgb(72, 179, 120);">Paxos算法是什么？</font>**
<font style="color:rgb(40, 202, 113);">Paxos</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">算法是</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">基于消息传递</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">且具有</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">高效容错特性</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">的一致性算法，目前公认的解决</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">分布式一致性问题</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">最有效的算法之一。</font>

### **<font style="color:rgb(72, 179, 120);">Paxos算法的工作流程？</font>**
#### <font style="color:black;">角色</font>
<font style="color:rgb(74, 74, 74);">在Paxos中有这么几个角色：</font>

1. **<font style="color:rgb(74, 74, 74);">Proposer（提议者）</font>****<font style="color:rgb(1, 1, 1);"> </font>****<font style="color:rgb(1, 1, 1);">: 提议者提出提案，用于投票表决。</font>**
2. **<font style="color:rgb(74, 74, 74);">Accecptor（接受者）</font>****<font style="color:rgb(1, 1, 1);"> </font>****<font style="color:rgb(1, 1, 1);">: 对提案进行投票，并接受达成共识的提案。</font>**
3. **<font style="color:rgb(74, 74, 74);">Learner（学习者）</font>****<font style="color:rgb(1, 1, 1);"> </font>****<font style="color:rgb(1, 1, 1);">: 被告知投票的结果，接受达成共识的提案。</font>**

<font style="color:rgb(74, 74, 74);">在实际中，一个节点可以同时充当不同角色。</font>

![1696574091668-8c453f37-6e0b-496e-8b63-dda586174498.png](./img/q5CzysxqwNHRRtv5/1696574091668-8c453f37-6e0b-496e-8b63-dda586174498-462333.png)

<font style="color:rgb(136, 136, 136);">Paxos的三种角色</font>

<font style="color:rgb(74, 74, 74);">提议者提出提案，提案=编号+value，可以表示为[M,V]，每个提案都有唯一编号，而且编号的大小是趋势递增的。</font>

#### <font style="color:black;">算法流程</font>
<font style="color:rgb(74, 74, 74);">Paxos算法包含两个阶段，第一阶段**Prepare(准备)</font>**<font style="color:rgb(74, 74, 74);">、第二阶段</font>**<font style="color:rgb(74, 74, 74);">Accept(接受)**。</font>

![1695892187691-49e874ad-fe63-49fc-a6ec-5d6051134e6e.png](./img/q5CzysxqwNHRRtv5/1695892187691-49e874ad-fe63-49fc-a6ec-5d6051134e6e-718453.png)

<font style="color:rgb(136, 136, 136);">Paxos算法流程</font>

##### <font style="color:black;">Prepare(准备)阶段</font>
1. **<font style="color:rgb(1, 1, 1);">提议者提议一个新的提案 P[Mn,?]，然后向接受者的某个超过半数的子集成员发送编号为Mn的准备请求</font>**
2. **<font style="color:rgb(1, 1, 1);">如果一个接受者收到一个编号为Mn的准备请求，并且编号Mn大于它已经响应的所有准备请求的编号，那么它就会将它已经批准过的最大编号的提案作为响应反馈给提议者，同时该接受者会承诺不会再批准任何编号小于Mn的提案。</font>**

<font style="color:rgb(74, 74, 74);">总结一下，接受者在收到提案后，会给与提议者</font>**<font style="color:rgb(74, 74, 74);">两个承诺</font>**<font style="color:rgb(74, 74, 74);">与</font>**<font style="color:rgb(74, 74, 74);">一个应答</font>**<font style="color:rgb(74, 74, 74);">：</font>

+ **<font style="color:rgb(1, 1, 1);">两个承诺：</font>**
    - **<font style="color:rgb(1, 1, 1);">承诺不会再接受提案号小于或等于 Mn 的 Prepare 请求</font>**
    - **<font style="color:rgb(1, 1, 1);">承诺不会再接受提案号小于Mn 的 Accept 请求</font>**
+ **<font style="color:rgb(1, 1, 1);">一个应答：</font>**
    - **<font style="color:rgb(1, 1, 1);">不违背以前作出的承诺的前提下，回复已经通过的提案中提案号最大的那个提案所设定的值和提案号Mmax，如果这个值从来没有被任何提案设定过，则返回空值。如果不满足已经做出的承诺，即收到的提案号并不是决策节点收到过的最大的，那允许直接对此 Prepare 请求不予理会。</font>**

##### <font style="color:black;">Accept(接受)阶段</font>
1. **<font style="color:rgb(1, 1, 1);">如果提议者收到来自半数以上的接受者对于它发出的编号为Mn的准备请求的响应，那么它就会发送一个针对[Mn,Vn]的接受请求给接受者，注意Vn的值就是收到的响应中编号最大的提案的值，如果响应中不包含任何提案，那么它可以随意选定一个值。</font>**
2. **<font style="color:rgb(1, 1, 1);">如果接受者收到这个针对[Mn,Vn]提案的接受请求，只要该接受者尚未对编号大于Mn的准备请求做出响应，它就可以通过这个提案。</font>**

<font style="color:rgb(74, 74, 74);">当提议者收到了多数接受者的接受应答后，协商结束，共识决议形成，将形成的决议发送给所有学习节点进行学习。</font>

<font style="color:rgb(74, 74, 74);">所以Paxos算法的整体详细流程如下：</font>

![1695892187582-f6c5442c-822b-4555-a9a6-89f58860f0c9.png](./img/q5CzysxqwNHRRtv5/1695892187582-f6c5442c-822b-4555-a9a6-89f58860f0c9-706685.png)

<font style="color:rgb(136, 136, 136);">Paxos详细流程</font>

<font style="color:rgb(74, 74, 74);">算法的流程模拟，可以查看参考[13]。</font>

### **<font style="color:rgb(72, 179, 120);">Paxos算法有什么缺点吗？怎么优化？</font>**
<font style="color:rgb(74, 74, 74);">前面描述的可以称之为Basic Paxos 算法，在单提议者的前提下是没有问题的，但是假如有多个提议者互不相让，那么就可能导致整个提议的过程进入了死循环。</font>

<font style="color:rgb(74, 74, 74);">Lamport 提出了 Multi Paxos 的算法思想。</font>

<font style="color:rgb(74, 74, 74);">Multi Paxos算法思想，简单说就是在多个提议者的情况下，选出一个Leader（领导者），由领导者作为唯一的提议者，这样就可以解决提议者冲突的问题。</font>



> 更新: 2023-10-06 14:35:10  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/sh5gb8mbgvo6alip>