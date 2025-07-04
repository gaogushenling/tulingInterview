# RocketMQ的消息存储如何进行清理和归档

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">RocketMQ 提供了消息存储清理和归档的机制，以便管理消息存储空间，删除过期消息，并将历史消息归档到其他存储介质中。这些功能有助于维护消息队列的性能和可用性。以下是关于 RocketMQ 消息存储清理和归档的主要方面：</font>

1. **<font style="background-color:rgb(247, 247, 248);">消息文件删除策略：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> RocketMQ 支持多种消息文件删除策略，可以在配置文件中进行设置。以下是一些常见的策略：</font>
+ **<font style="background-color:rgb(247, 247, 248);">定时删除策略：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 您可以配置 RocketMQ 定期删除过期的消息文件和索引文件。这样，一旦消息文件中的消息过期，RocketMQ 将自动删除它们。</font>
+ **<font style="background-color:rgb(247, 247, 248);">空间满策略：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 如果存储磁盘空间达到一定限制，RocketMQ 可以自动删除最早的消息文件，以释放磁盘空间。这个策略确保了存储空间不会无限制地增长。</font>
+ **<font style="background-color:rgb(247, 247, 248);">指定时间段删除策略：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 您可以配置 RocketMQ 只删除特定时间段内的消息文件，以保留历史消息。</font>
2. **<font style="background-color:rgb(247, 247, 248);">消息归档：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> RocketMQ 允许您将历史消息归档到其他存储介质中，以减小消息服务器的存储负担。归档通常涉及将消息转移到长期存储（如云存储或本地归档系统）中。归档可以手动触发，也可以自动触发，具体取决于您的需求。</font>
3. **<font style="background-color:rgb(247, 247, 248);">历史消息访问：</font>**<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);"> 尽管消息被归档，RocketMQ 仍然提供了访问历史消息的机制。通过合适的归档系统或者存储介质，您可以检索和访问历史消息，以满足合规性要求或其他业务需求。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">需要注意的是，清理和归档消息不是 RocketMQ 的核心功能，而是辅助功能。您需要根据自己的需求和业务场景来配置和管理消息的清理和归档策略。确保配置合理的清理策略以防止存储空间耗尽，并根据业务需求进行消息的归档操作，以保留历史消息数据。同时，归档后的消息可以根据需要进行合适的检索和恢复，以满足特定的数据需求。</font>



> 更新: 2023-09-12 13:44:47  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dsd9wiywh58904vg>