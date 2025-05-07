# DeepSeek本地部署教程

<font style="color:rgba(0, 0, 0, 0.9);">deepseek最近非常火，有时很卡顿，用不了，</font>**<font style="color:rgba(0, 0, 0, 0.9);">那么本地部署的需求也随之而来，很多人是有这个需求的。</font>**

<font style="color:rgba(0, 0, 0, 0.9);">其实很简单，几分钟就可以安装完。</font>

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>**<font style="color:rgba(0, 0, 0, 0.9);">🚀</font>****<font style="color:rgba(0, 0, 0, 0.9);"> 十分钟搞定！Windows电脑玩转DeepSeek本地部署</font>**

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>**<font style="color:rgba(0, 0, 0, 0.9);">🌈</font>****<font style="color:rgba(0, 0, 0, 0.9);"> 步骤一：安装灵魂工具Ollama</font>**<font style="color:rgba(0, 0, 0, 0.9);">  
</font><font style="color:rgba(0, 0, 0, 0.9);">▌官网下载直通车：</font><font style="color:rgba(0, 0, 0, 0.9);">👉</font><font style="color:rgba(0, 0, 0, 0.9);"> https://ollama.com  
</font>

![1738743300127-433b94b7-4509-4f69-bb6f-f7ebb5d66b53.png](./img/j7bFAx6rfeXrZshZ/1738743300127-433b94b7-4509-4f69-bb6f-f7ebb5d66b53-467966.png)

<font style="color:rgba(0, 0, 0, 0.9);">操作就像安装QQ一样简单，点击Download</font>

1. <font style="color:rgba(0, 0, 0, 0.9);">双击下载的.exe安装包</font>
2. <font style="color:rgba(0, 0, 0, 0.9);">狂点「下一步」直到完成</font>

![1738743300113-5a08960f-dc7f-4099-9644-59426dcb73ae.png](./img/j7bFAx6rfeXrZshZ/1738743300113-5a08960f-dc7f-4099-9644-59426dcb73ae-400993.png)

  
🌈** 步骤二：回到ollama的官网，搜索框里搜索deepseek-r1，选择要安装的模型**

![1738743300121-9d39cc88-ae53-4d76-a0ca-9e1de285d7f8.png](./img/j7bFAx6rfeXrZshZ/1738743300121-9d39cc88-ae53-4d76-a0ca-9e1de285d7f8-967429.png)<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

<font style="color:rgba(0, 0, 0, 0.9);">点击下拉框，可以看到多个版本，区别是参数不一样，数字越大，代表参数越多，性能就越强，但也对计算机的性能要求较高。</font>

![1738743300431-9f87126a-5d9b-490f-a6bc-9d02bc6b5918.png](./img/j7bFAx6rfeXrZshZ/1738743300431-9f87126a-5d9b-490f-a6bc-9d02bc6b5918-679857.png)

<font style="color:rgba(0, 0, 0, 0.9);">  
</font><font style="color:rgba(0, 0, 0, 0.9);">模型版本怎么选？电脑配置不行的，</font><font style="color:rgba(0, 0, 0, 0.9);">建议选择1.5B版本，这个模型有15亿参数，属于最轻量的</font><font style="color:rgba(0, 0, 0, 0.9);">Deep</font><font style="color:rgba(0, 0, 0, 0.9);">seek版本，电脑配置好点的，可以选择7b以上的。</font>

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>**<font style="color:rgba(0, 0, 0, 0.9);">🌈</font>****<font style="color:rgba(0, 0, 0, 0.9);"> 步骤三：复制右边的这串代码“ollama run deepseek-r1:1.5b”</font>**

![1738743300468-3b900f20-5c9d-4c0f-a6fd-c22d271a8fec.png](./img/j7bFAx6rfeXrZshZ/1738743300468-3b900f20-5c9d-4c0f-a6fd-c22d271a8fec-074046.png)

<font style="color:rgba(0, 0, 0, 0.9);">  
</font>**<font style="color:rgba(0, 0, 0, 0.9);">🌈</font>****<font style="color:rgba(0, 0, 0, 0.9);"> 步骤四：安装模型</font>**

<font style="color:rgba(0, 0, 0, 0.9);">按下键盘上的win+R，调出运行窗口，输入cmd回车，调出命令行窗口。</font>

![1738743300521-ef78c0de-206f-422c-a4ca-5424851467db.png](./img/j7bFAx6rfeXrZshZ/1738743300521-ef78c0de-206f-422c-a4ca-5424851467db-248056.png)<font style="color:rgba(0, 0, 0, 0.9);">  
</font>

**把复制的代码“ollama run deepseek-r1:1.5b”粘贴到命令行中，再点击回车，如下图所示。**

![1738743300641-d0069cec-63a2-4b88-9e33-8e1b2020a081.png](./img/j7bFAx6rfeXrZshZ/1738743300641-d0069cec-63a2-4b88-9e33-8e1b2020a081-554066.png)

按回车键之后，就会开始安装，会有百分比的进度条，如下图所示

![1738743300655-f5678259-13e7-47e1-8f9f-b0cb75ab8522.png](./img/j7bFAx6rfeXrZshZ/1738743300655-f5678259-13e7-47e1-8f9f-b0cb75ab8522-672096.png)

跑到了100%之后，就代表安装完成了，就可以和他对话了。

![1738743300734-6fe32845-556f-464e-ba5e-9eecb18faf4b.png](./img/j7bFAx6rfeXrZshZ/1738743300734-6fe32845-556f-464e-ba5e-9eecb18faf4b-928168.png)

  
**🌈**** 步骤四：安装可视化工具：chatbox**

是时候告别黑乎乎的窗口了！咱们请出颜值担当：

🔥 ChatBox客户端  
▌官网直达：https://chatboxai.app

![1738743300843-12bb2525-70cc-42f3-a774-a56c2c0317e4.png](./img/j7bFAx6rfeXrZshZ/1738743300843-12bb2525-70cc-42f3-a774-a56c2c0317e4-110502.png)

<font style="color:rgba(0, 0, 0, 0.9);">安装姿势：</font>

1. <font style="color:rgba(0, 0, 0, 0.9);">点击免费下载后，解压后双击ChatBoxSetup.exe</font>
2. <font style="color:rgba(0, 0, 0, 0.9);">自定义安装路径（别放C盘！建议装D盘）</font>
3. <font style="color:rgba(0, 0, 0, 0.9);">Chatbox安装好后，打开后，选择“使用自己的 API Key 或本地模型”。</font>

![1738743300852-2a616e68-87d9-41e5-8eb2-f83f6994243e.png](./img/j7bFAx6rfeXrZshZ/1738743300852-2a616e68-87d9-41e5-8eb2-f83f6994243e-390892.png)

  
点击右下角的设置，设置好模型，选择Ollama API，最后选择已经安装好的模型就可以了。

![1738743301011-df586ec5-d1c0-451b-a840-88ee4b61006f.png](./img/j7bFAx6rfeXrZshZ/1738743301011-df586ec5-d1c0-451b-a840-88ee4b61006f-765895.png)



> 更新: 2025-02-05 17:13:44  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/pgswxy26zlgb4eq9>