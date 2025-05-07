# 三分钟搞定！Windows本地部署DeepSeek-R1推理模型





**<font style="color:#000000;">步骤一：安装Ollama</font>**

<font style="color:#000000;">官网下载：</font><font style="color:#000000;">👉</font>[<font style="color:#003884;"> https://ollama.co</font>](https://ollama.com)m

![1740158306200-6c604e6f-264b-4509-a557-4bc802a745a2.png](./img/3udGnIVnHxQlObnT/1740158306200-6c604e6f-264b-4509-a557-4bc802a745a2-143041.png)

操作就像安装QQ一样简单，点击Download

双击下载的.exe安装包

![1740158306381-0a77a89e-3c78-4425-b6fb-07ed12d7b5f9.png](./img/3udGnIVnHxQlObnT/1740158306381-0a77a89e-3c78-4425-b6fb-07ed12d7b5f9-626950.png)

点「下一步」直到完成

![1740158306574-da8f201a-da19-4ef0-ab6e-81522e9fd0ab.png](./img/3udGnIVnHxQlObnT/1740158306574-da8f201a-da19-4ef0-ab6e-81522e9fd0ab-898637.png)

**<font style="color:#000000;">步骤二：回到ollama的官网，搜索框里搜索deepseek-r1，选择要安装的模型</font>**

[<font style="color:#003884;">https://ollama.com/library/deepseek-r1</font>](https://ollama.com/library/deepseek-r1)

<font style="color:#000000;">点击下拉框，可以看到多个版本，区别是参数不一样，数字越大，代表参数越多，性能就越强，但也对计算机的性能要求较高。</font>

![1740158306801-9f2d0c4b-205f-479d-b5a0-88f77f9a4c3e.png](./img/3udGnIVnHxQlObnT/1740158306801-9f2d0c4b-205f-479d-b5a0-88f77f9a4c3e-113828.png)

<font style="color:#000000;">模型版本怎么选？电脑配置不行的，建议选择1.5B版本，这个模型有15亿参数，属于最轻量的</font><font style="color:#000000;">Deepseek版本，电脑配置好点的，可以选择7b以上的。</font>

下面是不同模型推荐配置

![1740158306989-291a4ce6-12cc-4ab7-8034-94bfcfd2909c.png](./img/3udGnIVnHxQlObnT/1740158306989-291a4ce6-12cc-4ab7-8034-94bfcfd2909c-964987.png)



**<font style="color:#000000;">步骤三：复制右边的这串代码“</font>****<font style="color:#000000;">ollama run deepseek-r1:1.5b</font>****<font style="color:#000000;">”</font>**



![1740158307232-c3583006-ef80-4c24-945e-3345692fde41.png](./img/3udGnIVnHxQlObnT/1740158307232-c3583006-ef80-4c24-945e-3345692fde41-005896.png)

**步骤四：安装模型**

按下键盘上的win+R，调出运行窗口，输入cmd回车，调出命令行窗口。

![1740158307409-3248eb77-0ec0-45ea-ac0f-6add6c66ff13.png](./img/3udGnIVnHxQlObnT/1740158307409-3248eb77-0ec0-45ea-ac0f-6add6c66ff13-791962.png)

**把复制的代码“****ollama run deepseek-r1:1.5b****”粘贴到命令行中，再点击回车。**

<font style="color:#000000;">按回车键之后，就会开始安装，会有百分比的进度条，如下图所示</font>

![1740158307569-15a52bd3-70b6-4b79-b520-1fbc7a4ee644.png](./img/3udGnIVnHxQlObnT/1740158307569-15a52bd3-70b6-4b79-b520-1fbc7a4ee644-714319.png)

<font style="color:#000000;">跑到了100%之后，就代表安装完成了，就可以和他对话了。</font>

![1740158307742-f52fac18-07e8-401e-a718-f4cdfd59ca5a.png](./img/3udGnIVnHxQlObnT/1740158307742-f52fac18-07e8-401e-a718-f4cdfd59ca5a-927789.png)



**<font style="color:#000000;">步骤四：安装可视化工具：chatbox</font>**

<font style="color:#000000;">ChatBox客户端</font>

<font style="color:#000000;">官网直达：</font>[<font style="color:#003884;">https://chatboxai.app</font>](https://chatboxai.app)



![1740158308029-54d7c918-0af0-4b88-8d85-530bd03fd0e4.png](./img/3udGnIVnHxQlObnT/1740158308029-54d7c918-0af0-4b88-8d85-530bd03fd0e4-717460.png)



安装姿势：

点击免费下载后，解压后双击ChatBoxSetup.exe

自定义安装路径（别放C盘！建议装D盘）

![1740158308281-524469d2-752b-499d-9163-48ab4374ddc4.png](./img/3udGnIVnHxQlObnT/1740158308281-524469d2-752b-499d-9163-48ab4374ddc4-122595.png)

Chatbox安装好后，打开。

![1740158308479-97252ee9-134c-46ff-8a6c-d6c6c8298922.png](./img/3udGnIVnHxQlObnT/1740158308479-97252ee9-134c-46ff-8a6c-d6c6c8298922-587400.png)

<font style="color:#000000;">点击右下角的设置，设置好模型，选择Ollama API，最后选择已经安装好的模型就可以了。</font>

![1740158308663-dfcd6e0e-142e-4507-84eb-7fa871641705.png](./img/3udGnIVnHxQlObnT/1740158308663-dfcd6e0e-142e-4507-84eb-7fa871641705-220214.png)

点击保存后就可以对话了

![1740158308867-d8edec1f-a582-4dbd-9094-6ede7cbaa3d5.png](./img/3udGnIVnHxQlObnT/1740158308867-d8edec1f-a582-4dbd-9094-6ede7cbaa3d5-238132.png)



**拓展：使用硅基流动体验满血版deepseek**

**<font style="color:#404040;">SiliconFlow</font>**<font style="color:#404040;"> 联合 </font>**<font style="color:#404040;">华为昇腾</font>**<font style="color:#404040;">（国产芯片）推出了 </font>**<font style="color:#404040;">DeepSeek-R1</font>**<font style="color:#404040;"> 和 </font>**<font style="color:#404040;">DeepSeek-V3</font>**<font style="color:#404040;"> 的 </font>**<font style="color:#404040;">671B 满血版 API 服务</font>**<font style="color:#404040;">，响应速度与官方 API 基本一致，性能强劲且稳定。当前SiliconFlow正在做活动，注册即送 2000 万 Tokens（</font><font style="color:#18191C;">14 元平台额度</font><font style="color:#404040;">），链接：</font>[<font style="color:#003884;">https://cloud.siliconflow.cn/i/wn4Ok7Iz</font>](https://cloud.siliconflow.cn/i/wn4Ok7Iz)

登录之后，直接就进入【模型广场】了。排在第一位的模型就是**<font style="color:#404040;">DeepSeek-R1</font>**<font style="color:#404040;"> </font>。

![1740158309170-8e83824d-7f7d-4d28-80c4-8c1e0eb6700a.png](./img/3udGnIVnHxQlObnT/1740158309170-8e83824d-7f7d-4d28-80c4-8c1e0eb6700a-916187.png)

但是你不用管，直接看最左边的导航栏，找到【API密钥】，点进去，再点右上角的新建API密钥。<font style="color:#000000;">新建完成之后，你就会得到一个看着是加密的API Key了，这就是你的密钥，点击密钥那块就能复制。</font>

![1740158309475-6ceb240d-b0d2-4d12-bdcc-bd210f01813c.png](./img/3udGnIVnHxQlObnT/1740158309475-6ceb240d-b0d2-4d12-bdcc-bd210f01813c-070317.png)

**<font style="color:#000000;">配置Chatbox</font>**

<font style="color:#000000;">在Chatbox模型提供方里，找到这个SiliconFlow API，这个就是硅基流动的英文名。在API密钥里，输入上一步咱们复制下来的API key，直接粘贴进去，粘贴完以后，下面模型下拉框就会出现一堆数据了，全部都是硅基流动部署的模型，我们直接拉到后面选DeepSeek R1模型就行。</font>

![1740158309714-cd0e0ecd-982e-46b1-9af1-72c0cd8ca450.png](./img/3udGnIVnHxQlObnT/1740158309714-cd0e0ecd-982e-46b1-9af1-72c0cd8ca450-435136.png)



点击保存后就可以对话了

![1740158309889-a3d2dffc-3a79-4992-a30c-c22305738ae2.png](./img/3udGnIVnHxQlObnT/1740158309889-a3d2dffc-3a79-4992-a30c-c22305738ae2-163951.png)





> 更新: 2025-02-23 20:19:32  
> 原文: <https://www.yuque.com/u12222632/as5rgl/roeu8czeulye5306>