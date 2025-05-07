# Kafka消费者分区策略

### <font style="color:rgb(51, 51, 51);">消费者分区策略</font>
<font style="color:rgb(51, 51, 51);">可以通过消费者参数</font>

**<font style="color:rgb(51, 51, 51);">partition.assignment.strategy</font>**

<font style="color:rgb(51, 51, 51);">设置分区分配给消费者的策略。默认为Range。允许自定义策略。</font>

#### **<font style="color:rgb(51, 51, 51);">Range（范围）</font>**
<font style="color:rgb(51, 51, 51);">把主题的连续分区分配给消费者。（如果分区数量无法被消费者整除、第一个消费者会分到更多分区）</font>

**<font style="color:rgb(51, 51, 51);">对每个Topic进行独立的分区分配</font>**<font style="color:rgb(51, 51, 51);">，首先对分区按照分区ID进行排序，然后订阅这个Topic的消费组的消费者再进行排序，尽量均衡地将分区分配给消费者。这里的“尽量均衡”是因为分区数可能无法被消费者数量整除，导致某些消费者可能会多分配到一些分区</font>

<font style="color:rgb(51, 51, 51);">它的特点是以topic为主进行划分的，通过partition数/consumer数来决定每个消费者消费几个分区。如果有余则交给消费者1</font>

<font style="color:rgb(51, 51, 51);">假设消费者数量为N，主题分区数量为M，则有当前主题分配数量 = M%N==0? M/N +1 : M/N ;</font>

<font style="color:rgb(51, 51, 51);">简单来说就是将主题中的分区除以group中订阅此主题的消费者，除数有余则一号多分配。</font>

![1737698386440-6570cf2b-5cd3-4cd1-a61c-b43b25823387.png](./img/5ab67-dCWffn22Ll/1737698386440-6570cf2b-5cd3-4cd1-a61c-b43b25823387-293876.png)

<font style="color:rgb(51, 51, 51);">Range策略的缺点在于如果Topic足够多、且分区数量不能被平均分配时，会出现消费过载的情景，举一个例子</font>

![1737698386482-409ac9d7-b4aa-4781-badc-81d1978574bc.png](./img/5ab67-dCWffn22Ll/1737698386482-409ac9d7-b4aa-4781-badc-81d1978574bc-229243.png)

<font style="color:rgb(51, 51, 51);">可以看到此种情况已经相差3个分区，如果主题进一步扩大差距会愈发明显。</font>

#### **<font style="color:rgb(51, 51, 51);">RoundRobin（轮询）</font>**
<font style="color:rgb(51, 51, 51);">把主题的分区循环分配给消费者。</font>

<font style="color:rgb(51, 51, 51);">一种轮询式的分配策略，即每个人都会得到一个分区，顺序取决于他们注册时的顺序。这有助于确保所有消费者都能访问到所有数据。</font>

<font style="color:rgb(51, 51, 51);">简单来说就是把所有partition和所有consumer列出来，然后按照hashcode排序，最后进行轮询算法分配。</font>

<font style="color:rgb(51, 51, 51);">如果主题中分区不一样的时候如下：</font>

![1737698386482-bc0967df-0058-47e0-847f-1fec0e2198a9.png](./img/5ab67-dCWffn22Ll/1737698386482-bc0967df-0058-47e0-847f-1fec0e2198a9-784729.png)

<font style="color:rgb(51, 51, 51);">不难看出轮询策略是将partition当做最小分配单位，将所有topic的partition都看作一个整体。然后为消费者轮询分配partition。当然得到此结果的前提是Consumer Group种的消费者订阅信息是一致的，如果订阅信息不一致，得到的结果也不均匀，下面举个例子：</font>

![1737698386465-61354ddf-dec8-4290-bd70-189205b0009b.png](./img/5ab67-dCWffn22Ll/1737698386465-61354ddf-dec8-4290-bd70-189205b0009b-170534.png)

<font style="color:rgb(51, 51, 51);">如图，Consumer0订阅Topic-A、B，Consumer1订阅Topic-B、C</font>

<font style="color:rgb(51, 51, 51);">顺序注意图中的Seq，先分配TopicA</font>

<font style="color:rgb(51, 51, 51);">第一轮 : Consumer-0: Topic-A-Partition0</font>

<font style="color:rgb(51, 51, 51);">由于Consumer-1没有订阅Topic-A，所以只能找到Topic-B给Consumer-1分配</font>

<font style="color:rgb(51, 51, 51);">于是 Consumer-1: Topic-B-Partition0</font>

---

<font style="color:rgb(51, 51, 51);">第二轮: Consumer-0: Topic-A-Partition0,</font>**<font style="color:rgb(51, 51, 51);">Topic-A-Partition1</font>**

<font style="color:rgb(51, 51, 51);">Consumer-1: Topic-B-Partition0,</font>**<font style="color:rgb(51, 51, 51);">Topic-B-Partition1</font>**

---

<font style="color:rgb(51, 51, 51);">第三轮: Consumer-0: Topic-A-Partition0,Topic-A-Partition1，</font>**<font style="color:rgb(51, 51, 51);">Topic-A-Partition2</font>**

<font style="color:rgb(51, 51, 51);">Consumer-1: Topic-B-Partition0,Topic-B-Partition1，</font>**<font style="color:rgb(51, 51, 51);">Topic-B-Partition2</font>**

---

<font style="color:rgb(51, 51, 51);">第四、五、六轮：</font>

<font style="color:rgb(51, 51, 51);">Consumer-0: Topic-A-Partition0,Topic-A-Partition1，Topic-A-Partition2</font>

<font style="color:rgb(51, 51, 51);">Consumer-1: Topic-B-Partition0,Topic-B-Partition1，Topic-B-Partition2,</font>**<font style="color:rgb(51, 51, 51);">Topic-C-Partition-0,Topic-C-Partition-1,Topic-C-Partition-2</font>**

<font style="color:rgb(51, 51, 51);">可以看到Consumer-1多消费了3个分区。所以在Consumer Group有订阅消息不一致的情况下，我们尽量不要选用RoundRobin。</font>

<font style="color:rgb(51, 51, 51);">注意：上面介绍的两种分区分配方式,多多少少都会有一些分配上的偏差, 而且每次</font>**<font style="color:rgb(51, 51, 51);">重新分配</font>**<font style="color:rgb(51, 51, 51);">的时候都是把所有的都</font>**<font style="color:rgb(51, 51, 51);">重新来计算并分配</font>**<font style="color:rgb(51, 51, 51);">一遍, 那么每次分配的结果都会偏差很多, 如果我们在计算的时候能够考虑上一次的分配情况,来尽量的减少分配的变动,这样我们将尽可能地撤销更少的分区，因为撤销过程是昂贵的</font>

#### <font style="color:rgb(51, 51, 51);">StickyAssignor(粘性)</font>
<font style="color:rgb(51, 51, 51);">粘性分区：每一次分配变更相对上一次分配做最少的变动.</font>

<font style="color:rgb(51, 51, 51);">当某个消费者的某个分区出现故障或不可用时，它会尝试保留它已经分配但尚未处理的分区。如果其他消费者也出现了问题，则会尽力维持原先的分区分配。这意味着一旦某个分区被分配给了某个消费者，除非该消费者退出或者分区不可用，否则不会重新分配给其他消费者。</font>

<font style="color:rgb(51, 51, 51);">目标：</font>

<font style="color:rgb(51, 51, 51);">1、</font>**<font style="color:rgb(51, 51, 51);">分区的分配尽量的均衡，分配给消费者者的主题分区数最多相差一个；</font>**

<font style="color:rgb(51, 51, 51);">2、</font>**<font style="color:rgb(51, 51, 51);">每一次重分配的结果尽量与上一次分配结果保持一致</font>**

<font style="color:rgb(51, 51, 51);">当这两个目标发生冲突时，优先保证第一个目标</font>

<font style="color:rgb(51, 51, 51);">首先, </font>**<font style="color:rgb(51, 51, 51);">StickyAssignor粘性分区</font>**<font style="color:rgb(51, 51, 51);">在进行分配的时候,是以**RoundRobin的分配逻辑来计算的,但是它又弥补了RoundRobinAssignor的一些可能造成不均衡的弊端。</font>

<font style="color:rgb(51, 51, 51);">比如在讲RoundRobin弊端的那种case, 但是在StickyAssignor中就是下图的分配情况</font>

<font style="color:rgb(51, 51, 51);">把RoundRobinAssignor的弊端给优化了</font>

![1737698386440-25c26563-92d9-447c-91f3-198d74e5cb52.png](./img/5ab67-dCWffn22Ll/1737698386440-25c26563-92d9-447c-91f3-198d74e5cb52-549706.png)

<font style="color:rgb(51, 51, 51);">此时的结果明显非常不均衡，如果使用Sticky策略的话结果应该是如此：</font>

![1737698387019-5fa2413b-0458-45c8-8d00-38e1a61b67d4.png](./img/5ab67-dCWffn22Ll/1737698387019-5fa2413b-0458-45c8-8d00-38e1a61b67d4-466634.png)

<font style="color:rgb(51, 51, 51);">在这里我给出实际测试结果参考</font>

<font style="color:rgb(51, 51, 51);">比如有3个消费者（C0、C1、C2）、4个topic(T0、T1、T2、T3)，每个topic有2个分区（P1、P2）</font>

![1737698387210-8adc9209-ce1e-4692-bdf6-47f8e8a77df7.png](./img/5ab67-dCWffn22Ll/1737698387210-8adc9209-ce1e-4692-bdf6-47f8e8a77df7-390512.png)

**<font style="color:rgb(51, 51, 51);">C0:</font>****<font style="color:rgb(51, 51, 51);">T0P0、T1P1、T3P0</font>**

**<font style="color:rgb(51, 51, 51);">C1: T0P1、T2P0、T3P1</font>**

**<font style="color:rgb(51, 51, 51);">C2: T1P0、T2P1</font>**

<font style="color:rgb(51, 51, 51);">如果C1下线 、如果按照RoundRobin</font>

![1737698387198-4a952bd3-3433-4bfe-802f-2ec4956e69f5.png](./img/5ab67-dCWffn22Ll/1737698387198-4a952bd3-3433-4bfe-802f-2ec4956e69f5-611249.png)

**<font style="color:rgb(51, 51, 51);">C0:</font>****<font style="color:rgb(51, 51, 51);">T0P0、T1P0、T2P0、T3P0</font>**

**<font style="color:rgb(51, 51, 51);">C2: T0P1、T1P1、T2P1、T3P1</font>**

<font style="color:rgb(51, 51, 51);">对比之前</font>

![1737698387219-71f04c56-a167-4fa9-813e-8bacd7ba0124.png](./img/5ab67-dCWffn22Ll/1737698387219-71f04c56-a167-4fa9-813e-8bacd7ba0124-155703.png)

<font style="color:rgb(51, 51, 51);">如果C1下线 、如果按照StickyAssignor</font>

![1737698387219-c1949ab6-dbac-4e9b-9c65-d2109b393280.png](./img/5ab67-dCWffn22Ll/1737698387219-c1949ab6-dbac-4e9b-9c65-d2109b393280-343330.png)

**<font style="color:rgb(51, 51, 51);">C0:</font>****<font style="color:rgb(51, 51, 51);">T0P0、T1P1、T2P0、T3P0</font>**

**<font style="color:rgb(51, 51, 51);">C2: T0P1、T1P0、T2P1、T3P1</font>**

<font style="color:rgb(51, 51, 51);">对比之前</font>

![1737698387423-083016cc-eb27-45e1-90df-841072232608.png](./img/5ab67-dCWffn22Ll/1737698387423-083016cc-eb27-45e1-90df-841072232608-806793.png)

![1737698387618-031a6cb0-e8ae-4a80-a39f-374f67aa0e77.png](./img/5ab67-dCWffn22Ll/1737698387618-031a6cb0-e8ae-4a80-a39f-374f67aa0e77-505862.png)

#### <font style="color:rgb(51, 51, 51);">自定义策略</font>
<font style="color:rgb(51, 51, 51);">extends 类AbstractPartitionAssignor，然后在消费者端增加参数：</font>

<font style="color:rgb(51, 51, 51);">properties.put(ConsumerConfig.PARTITION_ASSIGNMENT_STRATEGY_CONFIG,类.class.getName());</font>

<font style="color:rgb(51, 51, 51);">即可。</font>

### <font style="color:rgb(51, 51, 51);">消费者分区策略源码分析</font>
<font style="color:rgb(51, 51, 51);">接着上个章节的代码。</font>

![1737698387649-76de593e-3659-490c-a7c3-2a65c066fe60.png](./img/5ab67-dCWffn22Ll/1737698387649-76de593e-3659-490c-a7c3-2a65c066fe60-333321.png)

![1737698387663-d935abaf-6b26-42ed-889f-3e7e97c55e79.png](./img/5ab67-dCWffn22Ll/1737698387663-d935abaf-6b26-42ed-889f-3e7e97c55e79-556921.png)

![1737698387687-be16f452-38a5-4a6a-ae98-828eea5220ff.png](./img/5ab67-dCWffn22Ll/1737698387687-be16f452-38a5-4a6a-ae98-828eea5220ff-579772.png)

![1737698387840-433e39b0-ffdf-4937-b274-e4d0e40123c1.png](./img/5ab67-dCWffn22Ll/1737698387840-433e39b0-ffdf-4937-b274-e4d0e40123c1-820015.png)

![1737698388054-fbc4879b-e659-4bf8-b344-4c9364d412a9.png](./img/5ab67-dCWffn22Ll/1737698388054-fbc4879b-e659-4bf8-b344-4c9364d412a9-206314.png)

## <font style="color:rgb(51, 51, 51);">Consumer拉取数据</font>
<font style="color:rgb(51, 51, 51);">这里就是拉取数据，核心Fetch类</font>

![1737698388084-8c62124d-35c1-4331-9ca3-25eaf66767e8.png](./img/5ab67-dCWffn22Ll/1737698388084-8c62124d-35c1-4331-9ca3-25eaf66767e8-992743.png)

![1737698388124-8a0b3e0b-2b72-411a-a3b5-0e7965b8d2c1.png](./img/5ab67-dCWffn22Ll/1737698388124-8a0b3e0b-2b72-411a-a3b5-0e7965b8d2c1-540512.png)

![1737698388121-022b53b2-074e-4d95-8b18-661506694506.png](./img/5ab67-dCWffn22Ll/1737698388121-022b53b2-074e-4d95-8b18-661506694506-658278.png)

## <font style="color:rgb(51, 51, 51);">自动提交偏移量</font>
![1737698388195-8ff532a6-8ca5-4c80-a1fc-1f1c35b36cdf.png](./img/5ab67-dCWffn22Ll/1737698388195-8ff532a6-8ca5-4c80-a1fc-1f1c35b36cdf-593721.png)

![1737698388425-dcfcd91d-5908-49bd-8eaf-ad3f6bc230da.png](./img/5ab67-dCWffn22Ll/1737698388425-dcfcd91d-5908-49bd-8eaf-ad3f6bc230da-964965.png)

![1737698388564-9b3e87f0-60e8-4132-bc8b-8ac4ac7c26ac.png](./img/5ab67-dCWffn22Ll/1737698388564-9b3e87f0-60e8-4132-bc8b-8ac4ac7c26ac-069451.png)

![1737698388572-a4710820-852a-4af9-9462-c38da3f0630d.png](./img/5ab67-dCWffn22Ll/1737698388572-a4710820-852a-4af9-9462-c38da3f0630d-003640.png)

![1737698388574-3e7f8da7-7bf5-4de0-8cc2-e19fc07e3b7b.png](./img/5ab67-dCWffn22Ll/1737698388574-3e7f8da7-7bf5-4de0-8cc2-e19fc07e3b7b-131555.png)

![1737698388673-489f6ff6-30b2-4ee2-865f-f56cee35504e.png](./img/5ab67-dCWffn22Ll/1737698388673-489f6ff6-30b2-4ee2-865f-f56cee35504e-154074.png)

![1737698388841-1c40369e-f3ae-42d0-8ca6-d6d93de5f7ef.png](./img/5ab67-dCWffn22Ll/1737698388841-1c40369e-f3ae-42d0-8ca6-d6d93de5f7ef-559819.png)

![1737698389000-dad7af49-e063-44f3-924e-b2de7e44d5b5.png](./img/5ab67-dCWffn22Ll/1737698389000-dad7af49-e063-44f3-924e-b2de7e44d5b5-219963.png)

![1737698388993-86eb5c39-f464-428b-9572-97cc4dae31ed.png](./img/5ab67-dCWffn22Ll/1737698388993-86eb5c39-f464-428b-9572-97cc4dae31ed-013870.png)

<font style="color:rgb(51, 51, 51);">当然，自动提交auto.commit.interval.ms</font>

![1737698389103-7cbba0b2-dc2a-4008-9621-9936fe594644.png](./img/5ab67-dCWffn22Ll/1737698389103-7cbba0b2-dc2a-4008-9621-9936fe594644-529217.png)

<font style="color:rgb(51, 51, 51);">默认5s</font>

![1737698389075-5629b034-2c06-41e4-9717-b42374e13d4d.png](./img/5ab67-dCWffn22Ll/1737698389075-5629b034-2c06-41e4-9717-b42374e13d4d-502603.png)

<font style="color:rgb(51, 51, 51);">从源码上也可以看出</font>

<font style="color:rgb(51, 51, 51);">maybeAutoCommitOffsetsAsync 最后这个就是poll的时候会自动提交，而且没到auto.commit.interval.ms间隔时间也不会提交，如果没到下次自动提交的时间也不会提交。</font>

<font style="color:rgb(51, 51, 51);">这个autoCommitIntervalMs就是auto.commit.interval.ms设置的</font>



> 更新: 2025-01-24 13:59:58  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/pxt0lionnhv3mfab>