# RocketMQ消息是如何存储的

<font style="color:rgba(0, 0, 0, 0.82);">RocketMQ的消息存储是一个复杂而高效的过程，设计上充分考虑了性能和扩展性。消息存储的主要组件包括 CommitLog 文件、消费队列文件（ConsumeQueue）、以及索引文件（IndexFile）。我们来详细解释一下这几个核心部分。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">1. CommitLog文件</font>
<font style="color:rgba(0, 0, 0, 0.82);">CommitLog是RocketMQ的核心存储文件，负责保存消息的完整内容。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">顺序写入</font>**<font style="color:rgba(0, 0, 0, 0.82);">：所有的消息都顺序写入CommitLog文件，这种方式减少了磁盘寻道时间，提高了写入性能。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">文件滚动</font>**<font style="color:rgba(0, 0, 0, 0.82);">：CommitLog按照固定大小（比如1GB）进行分片。当一个文件写满后，会创建一个新的文件。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">存储所有数据</font>**<font style="color:rgba(0, 0, 0, 0.82);">：包括消息体、主题、队列ID等。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">2. ConsumeQueue文件</font>
<font style="color:rgba(0, 0, 0, 0.82);">ConsumeQueue是针对消息的逻辑视图，旨在加快消费者对消息的访问速度。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">条目固定</font>**<font style="color:rgba(0, 0, 0, 0.82);">：每个ConsumeQueue条目固定为20字节，包含消息在CommitLog中的偏移量、消息大小、Tag哈希值。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">独立文件</font>**<font style="color:rgba(0, 0, 0, 0.82);">：每个主题的每个队列都有独立的ConsumeQueue文件，文件路径为</font>`<font style="color:rgba(0, 0, 0, 0.82);">store/consumequeue/{topic}/{queueId}</font>`<font style="color:rgba(0, 0, 0, 0.82);">。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">快速定位</font>**<font style="color:rgba(0, 0, 0, 0.82);">：通过ConsumeQueue，消费者无需扫描整个CommitLog即可快速找到消息的位置。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">3. IndexFile文件</font>
<font style="color:rgba(0, 0, 0, 0.82);">IndexFile用于支持消息的快速检索。</font>

+ **<font style="color:rgba(0, 0, 0, 0.82);">哈希索引</font>**<font style="color:rgba(0, 0, 0, 0.82);">：为消息的key建立哈希索引，支持通过key快速检索消息偏移。</font>
+ **<font style="color:rgba(0, 0, 0, 0.82);">增强查询</font>**<font style="color:rgba(0, 0, 0, 0.82);">：IndexFile是可选的，用于需要基于消息属性进行快速查找的场景。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">消息存储流程</font>
1. **<font style="color:rgba(0, 0, 0, 0.82);">接收消息</font>**<font style="color:rgba(0, 0, 0, 0.82);">：Broker接收到消息后，将其放入内存缓冲区（待写入CommitLog）。</font>
2. **<font style="color:rgba(0, 0, 0, 0.82);">写入CommitLog</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">每条消息追加到当前活跃的CommitLog文件中。使用顺序写入提升写入效率和磁盘利用率。</font>
3. **<font style="color:rgba(0, 0, 0, 0.82);">同步到ConsumeQueue</font>**<font style="color:rgba(0, 0, 0, 0.82);">：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">异步转发服务（ReputMessageService）从CommitLog读取新写入的消息。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">将消息的偏移量和其他元数据（如大小和Tag哈希值）存储到相应的ConsumeQueue文件中。</font>
4. **<font style="color:rgba(0, 0, 0, 0.82);">更新IndexFile</font>**<font style="color:rgba(0, 0, 0, 0.82);">（可选）：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">若消息带有key（如业务ID），则将其哈希和偏移量存入IndexFile。</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">这样，可以通过该key快速查找消息。</font>



> 更新: 2024-08-18 22:13:53  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ac2mebudgnn2genu>