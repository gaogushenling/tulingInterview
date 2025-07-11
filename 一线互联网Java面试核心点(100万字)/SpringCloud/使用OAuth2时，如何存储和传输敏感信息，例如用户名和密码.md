# 使用OAuth2时，如何存储和传输敏感信息，例如用户名和密码

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">使用OAuth2时，不建议直接存储和传输敏感信息，比如用户名和密码。这是由于OAuth2协议自身的设计，它鼓励使用临时凭证（例如访问令牌和刷新令牌）进行安全地授权和认证，而不是直接使用敏感的用户信息。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">以下是使用OAuth2时存储和传输敏感信息的常见做法：</font>

1. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">用户登录并授权</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：用户在客户端应用程序（比如网站或移动应用）上输入他们的用户名和密码，然后客户端应用程序将用户提交的信息发送到授权服务器进行验证。如果用户信息验证成功，授权服务器将生成一个访问令牌并将其发送回客户端应用程序。</font>
2. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">访问令牌的使用</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：客户端应用程序存储访问令牌，并将其包含在每个请求中，以证明它们的身份。然而，客户端应用程序不应存储敏感的用户名和密码信息。</font>
3. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">刷新令牌的使用</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：访问令牌通常有一个有限的生命周期，当它过期时，客户端应用程序可以使用刷新令牌从授权服务器获取一个新的访问令牌。同样地，客户端应用程序不应存储敏感的用户名和密码信息。</font>
4. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">敏感信息的存储</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：敏感的用户信息（如密码）应存储在安全的环境中，如数据库或密钥管理系统中。这些信息应被适当地加密和保护，以防止未经授权的访问。</font>
5. **<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">安全的通信协议</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">：在传输敏感信息和访问令牌时，应使用安全的通信协议（如HTTPS）以确保信息不被拦截或篡改。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">以上是一般的做法，但实际操作中可能还需要考虑其他因素，比如数据的备份和恢复策略、对第三方服务的信任程度等。</font>



> 更新: 2023-09-11 15:05:20  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/gu2sx82aq99rqpxq>