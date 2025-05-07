# OAuth2有哪几种授权模式

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">OAuth2的授权模式包括以下四种：</font>

1. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">授权码模式</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：这是最常用且安全相最高的授权模式。在具有后端服务器web客户端的环境中，token令牌保存在客户端后端，对资源服务器访问在后端完成，可以有效避免token泄露。</font>
2. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">隐式授权模式/简化模式</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：这种模式下，用户的授权码直接被返回到客户端，而不是在后端保存。隐式授权模式与简化模式在实际应用中具有较高的安全性。</font>
3. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">密码模式</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：在这种模式下，用户向客户端提供他们的密码，客户端用密码来获取访问令牌。这种模式在实现上最简单，但在安全性方面却存在一些问题，因为密码可能会被泄露。</font>
4. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">客户端凭证模式</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：在此模式下，客户端以自己的身份去访问资源服务器，这种模式在实现上也比较简单，但同样存在安全性的问题。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">以上是OAuth2的四种授权模式，具体选择哪种模式需要根据实际应用场景和安全需求来决定。</font>



> 更新: 2023-09-11 15:03:47  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/rceal5owdubavhah>