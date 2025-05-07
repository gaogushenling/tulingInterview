# VM虚拟机安装Centos7教程

# 1.前置准备
虚拟机软件：** ****<font style="color:#DF2A3F;">VMware Workstation Pro</font>**

![1731593123366-519ab1da-839a-4602-ab70-c1b733f5a85c.png](./img/DMrEkrp8uld7r2Vl/1731593123366-519ab1da-839a-4602-ab70-c1b733f5a85c-624156.png)

**<font style="color:#DF2A3F;">Centos7 ISO 文件</font>**

![1731593109638-5944d3f8-b751-4ef5-965b-083d2d8b230d.png](./img/DMrEkrp8uld7r2Vl/1731593109638-5944d3f8-b751-4ef5-965b-083d2d8b230d-394187.png).

**<font style="color:#DF2A3F;">xshell 工具，去官网搞一个，个人使用不要钱</font>**

![1731593919191-23de48b4-ffdb-4200-bd8c-acda6e1f049a.png](./img/DMrEkrp8uld7r2Vl/1731593919191-23de48b4-ffdb-4200-bd8c-acda6e1f049a-148054.png)

**<font style="color:#DF2A3F;">如果是从 0 开始，大家不要自己找对应的版本，找对应的班班小姐姐获取相应的软件以及 ISO。</font>**

**<font style="color:#DF2A3F;">已经趟过的坑就不用再浪费时间了，安装本来就是一个不太重要的东西。</font>**

# 2.VM 安装 Centos7 
按照图片操作

![1731593352408-4eae8312-9258-415f-8afd-d6275cf169a3.png](./img/DMrEkrp8uld7r2Vl/1731593352408-4eae8312-9258-415f-8afd-d6275cf169a3-940237.png)

![1731593394771-60458518-f497-442f-ac69-0e6267ef5a3f.png](./img/DMrEkrp8uld7r2Vl/1731593394771-60458518-f497-442f-ac69-0e6267ef5a3f-764895.png)![1731593475406-d6c5c002-4ae3-43c4-bc34-484da696dd0d.png](./img/DMrEkrp8uld7r2Vl/1731593475406-d6c5c002-4ae3-43c4-bc34-484da696dd0d-992921.png)![1731593598546-06fb6ba4-561a-4ebd-9d54-0fa8ddeba03b.png](./img/DMrEkrp8uld7r2Vl/1731593598546-06fb6ba4-561a-4ebd-9d54-0fa8ddeba03b-779915.png)![1731593614655-2a2c0ce7-6a22-4dfa-a1f4-779789ef62d3.png](./img/DMrEkrp8uld7r2Vl/1731593614655-2a2c0ce7-6a22-4dfa-a1f4-779789ef62d3-620891.png)![1731593648667-5bbf83fe-4b1b-4f1b-b322-1829cceff00e.png](./img/DMrEkrp8uld7r2Vl/1731593648667-5bbf83fe-4b1b-4f1b-b322-1829cceff00e-482193.png)![1731593704055-64e6cf1f-62a5-4bd4-9339-d683b2fd1cad.png](./img/DMrEkrp8uld7r2Vl/1731593704055-64e6cf1f-62a5-4bd4-9339-d683b2fd1cad-327122.png)

**<font style="color:#DF2A3F;">修改完硬件配置后，点击完成会自动启动 Centos7，默默地等待就行。</font>**

## <font style="color:#DF2A3F;">补充：</font>
![1731594347465-8f0bf5f8-1691-416f-8739-91892edd27ca.png](./img/DMrEkrp8uld7r2Vl/1731594347465-8f0bf5f8-1691-416f-8739-91892edd27ca-257163.png)![1731594421816-e5224c3d-96c3-411b-84c5-51edc4122b36.png](./img/DMrEkrp8uld7r2Vl/1731594421816-e5224c3d-96c3-411b-84c5-51edc4122b36-286005.png)

![1731594495493-6b880411-a769-4e9b-ba4a-c6733664f693.png](./img/DMrEkrp8uld7r2Vl/1731594495493-6b880411-a769-4e9b-ba4a-c6733664f693-894498.png)

## <font style="color:#DF2A3F;">继续：</font>
**安装好之后涨这个样子，如果不知道怎么配置网络就开启图形化，在来配置，不会占用多少资源的，开了也可以后续关掉。**

```java
-- 关闭图形化：
systemctl set-default multi-user.target

-- 开启图形化：
systemctl set-default graphical.target
```

**<font style="color:#DF2A3F;">登录账号时，选择 Not listed，然后 输入 root 账号，密码之前自己输入的</font>****<font style="color:#DF2A3F;"></font>**

![1731594553531-ce200085-29d1-4e57-bf4c-49cd7638c593.png](./img/DMrEkrp8uld7r2Vl/1731594553531-ce200085-29d1-4e57-bf4c-49cd7638c593-791262.png)![1731594816610-00cdcd97-6792-4524-8f58-3c69e8dc9c1c.png](./img/DMrEkrp8uld7r2Vl/1731594816610-00cdcd97-6792-4524-8f58-3c69e8dc9c1c-754958.png)![1731594790064-845ec1b1-31d8-485b-b39b-721f58caa257.png](./img/DMrEkrp8uld7r2Vl/1731594790064-845ec1b1-31d8-485b-b39b-721f58caa257-821925.png)![1731594970219-ba8f40d6-3dae-4920-b03d-b557ad98a675.png](./img/DMrEkrp8uld7r2Vl/1731594970219-ba8f40d6-3dae-4920-b03d-b557ad98a675-152131.png)![1731594901026-cb254541-cc0d-49a0-917f-2cc846734f4a.png](./img/DMrEkrp8uld7r2Vl/1731594901026-cb254541-cc0d-49a0-917f-2cc846734f4a-940865.png)![1731595029953-e2873767-7166-4df2-bf6b-51494fece14f.png](./img/DMrEkrp8uld7r2Vl/1731595029953-e2873767-7166-4df2-bf6b-51494fece14f-427719.png)

![1731595070652-675e351a-261f-47d0-a455-769d16b58544.png](./img/DMrEkrp8uld7r2Vl/1731595070652-675e351a-261f-47d0-a455-769d16b58544-016239.png)![1731595148932-ca8de650-a8e2-4d1e-93a8-ddc49c00a321.png](./img/DMrEkrp8uld7r2Vl/1731595148932-ca8de650-a8e2-4d1e-93a8-ddc49c00a321-427022.png)![1731595264330-eab7768a-6a4e-4fd4-a894-749d542dd303.png](./img/DMrEkrp8uld7r2Vl/1731595264330-eab7768a-6a4e-4fd4-a894-749d542dd303-473866.png)![1731595301708-66114d3d-ad45-496b-81f5-d8e0c4db8e0d.png](./img/DMrEkrp8uld7r2Vl/1731595301708-66114d3d-ad45-496b-81f5-d8e0c4db8e0d-349812.png)

**<font style="color:#DF2A3F;">至此 NAT 网络连接就已经搞定了。</font>**

**<font style="color:#DF2A3F;"></font>**

**<font style="color:#DF2A3F;"></font>**

**<font style="color:#DF2A3F;">不喜欢图形化，使用将上边关闭图形化的命令复制到 xshell 执行。</font>**

**<font style="color:#DF2A3F;">在 VM 中是没有办法复制的。</font>**

**<font style="color:#DF2A3F;"></font>**

**<font style="color:#DF2A3F;"></font>**

**<font style="color:#DF2A3F;">如果你需要使用其他方式配置网络，请联系，更新教程。</font>**

**<font style="color:#DF2A3F;"></font>**

**<font style="color:#DF2A3F;"></font>**

**<font style="color:#DF2A3F;">如果网络一直有问题，请使用补充部分的网络重置。</font>**



> 更新: 2024-11-14 22:44:06  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/wyvmp2h6ka9h90yf>