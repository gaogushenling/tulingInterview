# SkyWalking中的数据是如何收集和传输的

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">SkyWalking中的数据主要通过以下步骤进行收集和传输：</font>

1. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">数据采集</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：SkyWalking的探针（probe）会与应用程序进行集成，从而可以获取到应用程序运行时的各种数据，包括但不限于请求响应时间、调用链路、系统资源使用情况等。这些数据随后被发送到SkyWalking的数据收集器（backend）中。</font>
2. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">数据传输</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：SkyWalking的数据收集器接收到探针发送的数据后，会对数据进行一些必要的处理，例如数据清洗、过滤和汇总等。处理后的数据将被存储在SkyWalking的数据存储组件中，这一组件在SkyWalking的配置文件中被称为“database”。</font>
3. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">数据消费</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：SkyWalking的数据存储组件不仅可以将收集到的数据存储在本地，还可以将数据发送给其他系统进行进一步的处理和消费。例如，SkyWalking可以将数据发送给可视化组件（UI），从而可以在图形化界面中查看和监控应用程序的性能数据。SkyWalking还可以将数据发送给告警系统，以便在性能问题发生时及时通知相关人员。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">在SkyWalking的数据传输过程中，可能会使用到GRPC等协议进行高效的数据传输。而在数据消费阶段，SkyWalking也支持多种不同的数据处理和消费方式，具体方式可以在配置中进行设置。</font>



> 更新: 2023-09-11 15:07:12  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/dgv3u0dofn6o7tos>