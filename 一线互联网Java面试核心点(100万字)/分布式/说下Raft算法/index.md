# 说下Raft算法

### <font style="color:rgb(1, 1, 1);">Raft算法</font>**<font style="color:rgb(72, 179, 120);">Raft算法是什么？</font>**
<font style="color:rgb(40, 202, 113);">Raft</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">也是一个</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">一致性算法</font>**<font style="color:rgb(74, 74, 74);">，和</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(40, 202, 113);">Paxos</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">目标相同。但它还有另一个名字 -</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">易于理解的一致性算法</font>**<font style="color:rgb(74, 74, 74);">。</font><font style="color:rgb(40, 202, 113);">Paxos</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">和</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(40, 202, 113);">Raft</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">都是为了实现</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">一致性</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">产生的。这个过程如同选举一样，</font>**<font style="color:rgb(74, 74, 74);">参选者</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">需要说服</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">大多数选民</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">(Server) 投票给他，一旦选定后就跟随其操作。</font><font style="color:rgb(40, 202, 113);">Paxos</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">和</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(40, 202, 113);">Raft</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">的区别在于选举的</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">具体过程</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">不同。</font>

### **<font style="color:rgb(72, 179, 120);">Raft算法的工作流程？</font>**
#### <font style="color:black;">Raft算法的角色</font>
<font style="color:rgb(40, 202, 113);">Raft</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">协议将</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(40, 202, 113);">Server</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">进程分为三种角色：</font>

+ **<font style="color:rgb(74, 74, 74);">Leader（领导者）</font>**
+ **<font style="color:rgb(74, 74, 74);">Follower（跟随者）</font>**
+ **<font style="color:rgb(74, 74, 74);">Candidate（候选人）</font>**

<font style="color:rgb(74, 74, 74);">就像一个民主社会，领导者由跟随者投票选出。刚开始没有</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">领导者</font>**<font style="color:rgb(74, 74, 74);">，所有集群中的</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">参与者</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">都是</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">跟随者</font>**<font style="color:rgb(74, 74, 74);">。</font>

<font style="color:rgb(74, 74, 74);">那么首先开启一轮大选。在大选期间</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">所有跟随者</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">都能参与竞选，这时所有跟随者的角色就变成了</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">候选人</font>**<font style="color:rgb(74, 74, 74);">，民主投票选出领袖后就开始了这届领袖的任期，然后选举结束，所有除</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">领导者</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">的</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">候选人</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">又变回</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">跟随者</font>**<font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">服从领导者领导。</font>

<font style="color:rgb(74, 74, 74);">这里提到一个概念</font><font style="color:rgb(74, 74, 74);"> </font>**<font style="color:rgb(74, 74, 74);">「任期」</font>**<font style="color:rgb(74, 74, 74);">，用术语</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(40, 202, 113);">Term</font><font style="color:rgb(74, 74, 74);"> </font><font style="color:rgb(74, 74, 74);">表达。</font>

<font style="color:rgb(74, 74, 74);">三类角色的变迁图如下：</font>

![1696575725646-fbcb96b7-e852-4f36-b7a1-5f195b519e3a.png](./img/lFRRac3DXOKf_y55/1696575725646-fbcb96b7-e852-4f36-b7a1-5f195b519e3a-867374.png)

<font style="color:rgb(136, 136, 136);">Raft三种角色变迁图</font>

#### <font style="color:black;">Leader选举过程</font>
<font style="color:rgb(74, 74, 74);">Raft 使用心跳（heartbeat）触发Leader选举。当Server启动时，初始化为Follower。Leader向所有Followers周期性发送heartbeat。如果Follower在选举超时时间内没有收到Leader的heartbeat，就会等待一段随机的时间后发起一次Leader选举。</font>

<font style="color:rgb(74, 74, 74);">Follower将其当前term加一然后转换为Candidate。它首先给自己投票并且给集群中的其他服务器发送 RequestVote RPC 。结果有以下三种情况：</font>

+ **<font style="color:rgb(1, 1, 1);">赢得了多数（超过1/2）的选票，成功选举为Leader；</font>**
+ **<font style="color:rgb(1, 1, 1);">收到了Leader的消息，表示有其它服务器已经抢先当选了Leader；</font>**
+ **<font style="color:rgb(1, 1, 1);">没有Server赢得多数的选票，Leader选举失败，等待选举时间超时（</font>****<font style="color:rgb(40, 202, 113);">Election Timeout</font>****<font style="color:rgb(1, 1, 1);">）后发起下一次选举。</font>**

![1696575725669-200f7dae-526a-4d0f-bfdf-6cef1fb7896b.png](./img/lFRRac3DXOKf_y55/1696575725669-200f7dae-526a-4d0f-bfdf-6cef1fb7896b-972391.png)

<font style="color:rgb(136, 136, 136);">Leader选举</font>

<font style="color:rgb(74, 74, 74);">选出 </font><font style="color:rgb(40, 202, 113);">Leader</font><font style="color:rgb(74, 74, 74);"> 后，</font><font style="color:rgb(40, 202, 113);">Leader</font><font style="color:rgb(74, 74, 74);"> 通过 </font>**<font style="color:rgb(74, 74, 74);">定期</font>**<font style="color:rgb(74, 74, 74);"> 向所有 </font><font style="color:rgb(40, 202, 113);">Follower</font><font style="color:rgb(74, 74, 74);"> 发送 </font>**<font style="color:rgb(74, 74, 74);">心跳信息</font>**<font style="color:rgb(74, 74, 74);"> 维持其统治。若 </font><font style="color:rgb(40, 202, 113);">Follower</font><font style="color:rgb(74, 74, 74);"> 一段时间未收到 </font><font style="color:rgb(40, 202, 113);">Leader</font><font style="color:rgb(74, 74, 74);"> 的 </font>**<font style="color:rgb(74, 74, 74);">心跳</font>**<font style="color:rgb(74, 74, 74);">，则认为 </font><font style="color:rgb(40, 202, 113);">Leader</font><font style="color:rgb(74, 74, 74);"> 可能已经挂了，然后再次发起 </font>**<font style="color:rgb(74, 74, 74);">选举</font>**<font style="color:rgb(74, 74, 74);"> 过程。</font>



> 更新: 2024-11-24 19:56:30  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ywiia55xxy96lkcg>