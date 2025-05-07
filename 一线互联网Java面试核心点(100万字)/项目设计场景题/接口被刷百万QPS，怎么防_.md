# 接口被刷百万QPS，怎么防?

  
**<font style="color:#000000;">分享内容</font>**

1. <font style="color:#000000;">接口被狂刷的严重后果</font>
2. <font style="color:#000000;">接口狂刷的主要防护措施(参数校验、资源授权、限流、IP封禁、日志监控等)</font>



## 接口被狂刷的严重后果
恶意攻击者通常会通过自动化工具进行攻击，尤其是会针对一些高频接口、核心接口进行恶意的访问，恶意的攻击，比如：

+ 注册登录接口
+ 秒杀抢购接口
+ 等等

接口被狂刷会带来很高的瞬时吞吐量，很容易超过 1Wqps，甚至 10WQPS。这样的超高并发，会导致系统的瞬时雪崩，严重的可能会导致线上系统 瘫痪。

## 接口狂刷的主要防护措施：
+ 交互式验证
+ 安全参数校验
+ 使用 HTTPS
+ 用户访问认证
+ 资源访问授权
+ 访问限流
+ IP 封禁
+ 日志监控和异步分析
+ 升级硬件设备
+ 基于时序的统计预警

### 交互式验证
主要包括：

+ **验证码验证**：在发送验证码之前，可以要求用户输入一个验证码，以验证用户的身份。这种方式可以有效地防止自动化攻击。
+ **人机验证**：人机验证是一种更高级的验证方式，可以检测用户行为是否类似于自动化攻击。例如，可以要求用户在发送验证码之前完成一个简单的任务，如拖动滑块或识别图片中的文字。

### 安全参数校验
当接口被恶意狂刷时，可以通过安全参数校验来防止这种攻击。安全参数校验是指在接口请求中添加一些校验参数，例如时间戳、随机字符串、签名等，来验证请求的合法性。这样可以防止攻击者通过恶意程序进行大量的请求攻击。

具体来说，可以通过以下步骤来实现安全参数校验：

1. 在接口请求中添加时间戳参数，例如：`timestamp=1622945123`。
2. 在接口请求中添加随机字符串参数，例如：`nonce=abc123`。
3. 将所有请求参数按照参数名的字母顺序排序，例如：`nonce=abc123&timestamp=1622945123`。
4. 将排序后的参数按照 “参数名 = 参数值” 的格式拼接成一个字符串，例如：`nonce=abc123&timestamp=1622945123`。
5. 将拼接后的字符串加上一个密钥（可以是预先约定好的密钥），例如：`nonce=abc123&timestamp=1622945123&key=secret`。
6. 对加密后的字符串进行哈希计算，例如使用 MD5 算法，得到一个签名值，例如：`c0c3f9a2a4c4c4dcd6d5b7b2a2e4d7b1`。将签名值添加到接口请求中，例如：`nonce=abc123&timestamp=1622945123&signature=c0c3f9a2a4c4c4dcd6d5b7b2a2e4d7b1`。
7. 在接口服务端对接口请求进行校验时，按照相同的算法计算签名值，并与请求中的签名值进行比对，如果一致，则说明请求合法，否则说明请求不合法。

通过以上步骤，可以有效地防止接口被恶意狂刷的攻击。

理论上，哈希计算很难破解，但是如果攻击者知道了 hash 算法和盐，攻击者就有可能伪造出带有正确校验位的签名值，从而绕过 Java 接口的限流和安全机制。

因此，该方案主要适用于需要简单防范一些低强度攻击的场景，例如防范垃圾请求或非法爬虫等。

对于高强度攻击，建议采取更为复杂的验证策略，例如使用使用用户访问认证，资源访问授权、IP 白名单、签名算法等。

### 使用 HTTPS
使用 HTTPS 可以保护数据传输的安全性，可以防止恶意攻击者窃取数据。HTTPS 使用 SSL/TLS 协议对数据进行加密，可以确保数据在传输过程中不被篡改或窃取。

因此，在一些恶意狂刷的高频接口，比如短信验证码接口、登录注册入口等敏感区域使用 HTTPS 是必要的。

当然，尽量在客户端和服务端在全部通讯，都使用 HTTPS 协议进行加密，防止数据被窃听或篡改。

### 用户访问认证
用户访问认证是指在系统中验证用户身份以授权其访问系统资源的过程。

用户访问认证是信息安全中非常重要的一环，可以保护系统免受未经授权的访问和攻击。

常见的用户访问认证方式包括用户名密码认证、双因素认证、证书认证等

这里来看最为简单的 用户访问认证： 用户名密码认证

用户名密码认证 的方式，要求用户提供 用户名和密码，换取 访问的令牌。

参考的代码如下：

```java
@RequestMapping("/api/login")
public String login(@RequestParam("username") String username, @RequestParam("password") String password){
    if(!checkUser(username, password)){
        return "用户名或密码错误";
    }
    String token = getToken();
    saveToken(token);
    return token;
}

private boolean checkUser(String username, String password){
    //校验用户是否合法
}

private String getToken(){
    //生成token
}

private void saveToken(String token){
//保存token
}


```

在上述代码中，当用户调用 login 接口时，需要提供用户名和密码。

此时会进行用户校验，若校验失败则返回错误信息，否则生成 token 并保存，最终返回给用户。

生成 Token 的作用是为了在接口请求时验证用户身份。

具体来说，当用户第一次登录系统后，该接口可以根据用户信息生成一个 Token 字符串，并将其保存至服务端或客户端。

+ 当此用户访问其他需要鉴权的接口时，需要在请求头中带上这个 Token 字符串，以便服务器进行身份验证。
+ 由于 Token 是由服务端生成的，攻击方无法自己生成有效的 Token，因此只有拥有合法 Token 的用户才能成功调用相关接口。

对于 Java 接口被恶意狂刷问题，Token 的作用是防止非法请求。

如果 Token 验证失败，则返回错误信息并拦截该请求。

+ 关于 Token 的验证，可以通过拦截器实现。
+ 拦截器可以在接口调用前检查请求头中是否包含合法的 Token，并验证 Token 是否过期、是否被篡改等。

下面是使用拦截器进行令牌校验的示例代码：

```java
// 鉴权拦截器
public class AuthInterceptor extends HandlerInterceptorAdapter {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
        String token = request.getHeader("Authorization");
        if (token == null || !checkToken(token)) {
            response.setStatus(HttpStatus.UNAUTHORIZED.value());
            return false;
        }
        return true;
    }

    private boolean checkToken(String token) {
        // 验证Token是否合法，是否过期等
    }
}


```

AuthInterceptor 类是拦截器类，用于检查请求头中的 Token 是否合法。

如果 Token 验证失败，则返回 401 错误码并拦截该请求。

### 资源访问授权
如果接口的安全性要求非常高，只有特定的用户才能访问。

或者说，如果遇到要对资源进行更细粒度的 防刷处理，可以对资源进行访问权限的管理和授权。 主要的策略有 RBAC 的机制。

主要的思路为：使用 访问控制策略 对资源权限进行 精准控制。场景的访问控制策略 为 RBAC 策略。

RBAC（Role-Based Access Control，基于角色的访问控制）是一种常见的访问控制策略，它将用户分配到不同的角色中，每个角色具有一组权限，从而控制用户对系统资源的访问。

在 RBAC 中，管理员可以根据用户的职责和职位，将用户分配到适当的角色中，从而控制用户对系统资源的访问权限。RBAC 还可以提高系统的安全性和可管理性，减少权限管理的复杂性。

Shiro 是一个强大且易于使用的 RBAC 访问的安全框架，提供了身份验证、授权、加密、会话管理等安全功能。其中授权是 Shiro 的核心功能之一，它可以帮助我们实现资源访问授权。

在 Shiro 中，授权是通过授权信息和角色信息来实现的。授权信息是指哪些用户可以访问哪些资源，角色信息是指用户可以拥有哪些权限。

Shiro 中的授权流程如下：

1. 用户登录系统，进行身份认证。
2. 身份验证成功后，Shiro 将用户信息存储在 Subject 中。
3. 用户请求访问某个资源。
4. Shiro 从 Subject 中获取用户信息，并根据用户信息和授权信息判断用户是否有权限访问该资源。
5. 如果用户有权限访问该资源，则允许访问；否则拒绝访问。

在 Shiro 中，授权信息和角色信息可以通过配置文件或数据库来管理。我们可以在配置文件或数据库中定义哪些用户可以访问哪些资源，以及哪些角色可以拥有哪些权限。在程序运行时，Shiro 会从配置文件或数据库中读取授权信息和角色信息，并根据这些信息进行授权判断。

除了配置文件和数据库，Shiro 还提供了编程式授权方式，即通过编写代码来实现授权。这种方式可以实现更加灵活的授权，但需要开发人员自己编写授权逻辑。

> 和 Shiro 类似，SpringSecurity 也是一个 资源访问授权的 框架。原理都是类似的，大家精通一个，另一个也触类旁通了。
>

### 访问限流
访问限流是一种常见的保护机制，用于控制对某个资源的访问速率，以防止过多的请求导致系统负载过高或崩溃。

访问限流包括两个维度：

+ 访问限流策略
+ 访问限流算法

维度一：访问限流策略

+ 面向接口限流
+ 面向用户限流

维度二：访问限流算法

+ 令牌桶算法
+ 漏桶算法

在令牌桶算法中，系统会按照一定速率往令牌桶中添加令牌，每个令牌代表一个请求的访问权限。当请求到来时，系统会从令牌桶中取出一个令牌，如果令牌桶中没有令牌，则拒绝该请求。

在漏桶算法中，系统会按照一定速率从漏桶中释放请求，当请求到来时，如果漏桶中还有空余容量，则将该请求放入漏桶中，否则拒绝该请求。

接口被恶意狂，可以使用 基于 漏桶算法 + 基于用户限流的 综合性限流策略。

可以结合黑名单策略，对恶意用户进行有效人工管理。 如果用户被限流，甚至可以加入黑名单，封掉这个用户。

### IP 封禁
IP 封禁是常见的网络安全措施，用于保护服务器免受恶意攻击。

IP 封禁是指将某个 IP 地址列入黑名单，禁止其访问服务器。

在实际应用中，可以通过配置防火墙规则、使用反向代理服务器、使用专业的防火墙软件等方式来实现 IP 封禁和防刷。

也可以在应用层代码中，通过过滤器的方式，进行 IP 封禁

参考代码如下：

```java
public class IpFilter extends OncePerRequestFilter {
    private static final Set<String> IP_SET = new HashSet<>();

    static {
        IP_SET.add("192.168.1.100");
        IP_SET.add("127.0.0.1");
        //添加其他需要封禁的IP
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {
        String ipAddress = request.getRemoteAddr();
        if(IP_SET.contains(ipAddress)){
            response.setStatus(HttpStatus.FORBIDDEN.value());
            return;
        }

        filterChain.doFilter(request, response);
    }
}


```

在上述代码中，通过 IpFilter 过滤器来阻止特定的 IP 地址访问接口。其中，IP_SET 为需要封禁的 IP 地址集合。

### 日志监控和异步分析
访问日志监控是一种常见的监控方式，用于监控网站、应用程序等的访问情况，可以帮助我们了解用户的行为和需求，以便做出相应的优化和改进。

常见的访问日志监控工具有 Apache 的 AccessLog、Nginx 的 AccessLog、ELK Stack 、Java 请求日志监控等。

这些工具、框架帮助我们收集、分析和可视化访问日志数据，从而更好地了解用户的需求和行为。

同时，我们也可以通过访问日志监控来检测和排查一些常见的安全问题，如 SQL 注入、XSS 攻击等。

监控访问日志可以帮助发现未经授权的访问请求。可以使用日志记录工具来记录每个请求的 IP 地址、时间戳和请求参数。

如果发现异常请求，可以及时采取措施，以防止攻击。

下面是一个参考的，进行响应记录的 过滤器。

```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
throws IOException, ServletException {
    HttpServletRequest httpRequest = (HttpServletRequest) request;
    HttpServletResponse httpResponse = (HttpServletResponse) response;
    String requestURI = httpRequest.getRequestURI();

    try {
        log.info("Request Received. URI: {}", requestURI);
        chain.doFilter(request, response);
    } catch(Exception e) {
        log.error("Exception occurred while processing request. URI: {}", requestURI, e);
        throw e;
    } finally {
        log.info("Request Completed. URI: {} Response Status: {}", requestURI, httpResponse.getStatus());
    }
}


```

在上述代码中，通过 Filter 过滤器来实现日志监控。

当请求进入时记录请求 URI，当请求结束时记录响应状态码，如此可及时发现异常情况。

有了日志仅仅是第一步，还需要结合定时任务或者 流式计算工具，进行异步分析，甚至是离线分析。

异步分析或者离线分析的，最终得到恶意请求的用户或者 ip，然后进行拉黑或者 IP 封禁。

### 升级硬件设备
如果服务器无法承受恶意攻击，可以通过升级硬件设备来增加服务器的承载能力。

例如，可以增加 CPU 或内存等硬件资源，降低服务器的响应时间。

### 基于时序的统计预警
当 Java 接口被恶意狂刷时，及时通知相关管理人员或安全团队是非常重要的。他们可以采取更加有效的措施，如封禁 IP 地址、加强认证机制等，从而保障接口的安全。

系统监控和预警通知是保持系统稳定和可靠性的重要手段。通常，我们需要对系统的各种指标进行监控和预警，如 CPU 使用率、内存使用率、磁盘空间、网络流量等等。当这些指标超过预设的阈值时，系统就会触发警报，并通知相关人员进行处理。

对于系统的 恶意狂刷，也可以基于 **时序进行** 统计和预警。

为了实现系统监控和预警通知，可以使用一些开源的工具，如 Prometheus、Grafana、Alertmanager 等等。

其中，Prometheus 是一个广泛使用的监控系统，它支持多种数据源，如本地文件、HTTP、JMX、SNMP 等等。

Grafana 则是一个可视化监控工具，它可以将 Prometheus 收集的数据进行可视化展示。

Alertmanager 则是一个通知管理器，它可以根据不同的警报级别和通知方式，将警报发送给不同的人员或团队。

结合 Prometheus +Grafana +Alertmanager ， 可以对 Java 接口被恶意狂刷 **进行时序统计**，一旦超过一定的阈值，比如 **1 分钟被狂刷 10W 次**，进行预警， 方便开发和运维进行防范。



> 更新: 2025-01-14 17:04:47  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/sabap6mrnfbv92gf>