# 接入AI网关Higress实现多个AI大模型的无缝切换，比如当deepseek调用失败，可以降级调用qwq-32b

<font style="color:rgb(6, 6, 7);">近期，通义千问发布了一款全新的推理模型 QwQ-32B。在各类基准测试中，这个拥有 320 亿参数的模型展现出了与 DeepSeek-R1（6710 亿参数）相当的性能。这意味着：</font>

+ <font style="color:rgb(6, 6, 7);">对于个人用户而言，QwQ-32B 能够直接在本地运行，且对设备的要求更低，适合在更小的移动设备上使用。</font>
+ <font style="color:rgb(6, 6, 7);">对于企业用户来说，调用推理大模型 API 的成本可以进一步降低，最高可减少 90% 的费用。</font>

<font style="color:rgb(6, 6, 7);">在实际应用中，无缝切换多个大模型的需求日益凸显。例如，企业可能需要同时对接多个大模型，以满足不同业务场景的需求；当单个模型出现稳定性问题时，能够迅速回退到另一个模型，确保业务的连续性和稳定性；在 Multi Agent 场景下，一个复杂任务可能需要调用多个模型来协同完成。Higress AI 网关提供了一个强大的解决方案，支持多模型服务，并具备消费者鉴权、模型自动切换等高级功能。</font>

![1741760832753-71421487-462c-4e10-9d26-05efdc8b6a6f.png](./img/0L-CuNkt1mXzFGRf/1741760832753-71421487-462c-4e10-9d26-05efdc8b6a6f-637647.png)

<font style="color:rgb(6, 6, 7);">本文将为您提供一份详细的教程，指导您如何使用 Higress AI 网关在 DeepSeek-R1 和 QwQ-32B 大模型之间实现无缝切换。通过以下步骤，您可以轻松完成这一目标：</font>

### 1. 环境准备
```plain
# 一键安装Higress（需Docker环境）
curl -sS https://higress.cn/ai-gateway/install.sh | bash
```

安装完成后访问控制台http://localhost:8001，完成初始化配置。

![1741669057034-48f3ecba-5aad-4237-b963-3b42d1f27a57.png](./img/0L-CuNkt1mXzFGRf/1741669057034-48f3ecba-5aad-4237-b963-3b42d1f27a57-643688.png)



![1741675267629-a36ec9a9-e665-40f3-8ba7-863013ac71bd.png](./img/0L-CuNkt1mXzFGRf/1741675267629-a36ec9a9-e665-40f3-8ba7-863013ac71bd-219614.png)

<font style="color:#DF2A3F;">注意：默认脚本是监听的127.0.0.1，如果想通过windows本地机器访问虚拟机中的higress，将ip替换为0.0.0.0</font>

![1741676552460-bae46f4b-c6db-4945-8c77-e4835fde791b.png](./img/0L-CuNkt1mXzFGRf/1741676552460-bae46f4b-c6db-4945-8c77-e4835fde791b-509689.png)



访问Higress控制台，首次访问需要设置管理员账号密码

![1741676439494-482b69c4-1b05-4ad9-aeee-38225d72f5cc.png](./img/0L-CuNkt1mXzFGRf/1741676439494-482b69c4-1b05-4ad9-aeee-38225d72f5cc-864868.png)

成功登录之后，会进入Higress控制台界面

![1741676825200-56304801-a94a-44d1-b427-ea699ba79de2.png](./img/0L-CuNkt1mXzFGRf/1741676825200-56304801-a94a-44d1-b427-ea699ba79de2-599184.png)



### <font style="color:rgb(6, 6, 7);">2.模型接入配置</font>
<font style="color:rgb(6, 6, 7);">在 Higress 控制台中，分别配置 DeepSeek-R1 和 QwQ-32B 的接入方式。对于厂商模型，选择相应的厂商名进行配置；对于自建模型，使用 OpenAI 兼容模式，填入 baseURL 即可。</font>

![1741763670749-2a82f2b0-7cd9-4d1e-b345-0d4511d5fcbf.png](./img/0L-CuNkt1mXzFGRf/1741763670749-2a82f2b0-7cd9-4d1e-b345-0d4511d5fcbf-142631.png)

<font style="color:rgb(6, 6, 7);">接下来，创建路由规则，按照匹配模型名称的方式转发给两个不同的模型。</font>

<font style="color:rgb(6, 6, 7);">例如，创建一个名为 </font>`aliyun`<font style="color:rgb(6, 6, 7);"> 的路由，匹配模型名称精确匹配 </font>`qwq-32b`<font style="color:rgb(6, 6, 7);">，转发给阿里云百炼平台的 QwQ-32B 模型服务；</font>

![1741763750212-e1820815-ec22-4849-8cff-52a8e38f8542.png](./img/0L-CuNkt1mXzFGRf/1741763750212-e1820815-ec22-4849-8cff-52a8e38f8542-662988.png)

<font style="color:rgb(6, 6, 7);">创建另一个名为 </font>`deepseek`<font style="color:rgb(6, 6, 7);"> 的路由，匹配模型名称精确匹配 </font>`deepseek-`<font style="color:rgb(6, 6, 7);">，转发给 DeepSeek平台服务。</font>

![1741763823855-cc658a01-39c1-4cef-aed8-00f3daa74870.png](./img/0L-CuNkt1mXzFGRf/1741763823855-cc658a01-39c1-4cef-aed8-00f3daa74870-680444.png)

<font style="color:rgb(6, 6, 7);"></font>

### 3.测试示例
统一访问Higress网关地址即可，可以通过设置model进行模型的切换

```plain
 base_url="http://Higress网关地址/v1"  
```

**示例**

```plain
curl 'http://192.168.65.185:8080/v1/chat/completions'       -H 'Content-Type: application/json'       -d '{
        "model": "qwen-turbo",
        "messages": [
          {
            "role": "user",
            "content": "你是谁!"
          }
        ]
      }'

```

![1741764615322-f31f01d2-60de-41c8-9966-3fe242d3471e.png](./img/0L-CuNkt1mXzFGRf/1741764615322-f31f01d2-60de-41c8-9966-3fe242d3471e-738284.png)

```plain
curl 'http://192.168.65.185:8080/v1/chat/completions'       -H 'Content-Type: application/json'       -d '{
        "model": "deepseek-chat",
        "messages": [
          {
            "role": "user",
            "content": "你是谁!"
          }
        ]
      }'
```

![1741764095497-3611b6fd-e457-420f-a17c-4a02069b36b0.png](./img/0L-CuNkt1mXzFGRf/1741764095497-3611b6fd-e457-420f-a17c-4a02069b36b0-454810.png)



higress添加消费者认证后，测试

```plain
curl 'http://192.168.65.185:8080/v1/chat/completions'       -H 'Content-Type: application/json'   -H 'Authorization: Bearer cbfda275-56ff-4d45-a984-6482502e2f25'    -d '{
        "model": "qwen-max",
        "messages": [
          {
            "role": "user",
            "content": "你是谁"
          }
        ]
      }'

```

![1741764280056-eeb96dfe-d2a2-4306-b055-628b2c5a5a98.png](./img/0L-CuNkt1mXzFGRf/1741764280056-eeb96dfe-d2a2-4306-b055-628b2c5a5a98-869184.png)



### 4.Spring Ai整合Higress实现多模型无缝切换
**1）引入依赖**

<font style="color:rgb(53, 56, 65);">在项目中接入具有 OpenAI API 规范的大模型时，只需要引入 </font>`<font style="color:rgb(53, 56, 65);">spring-ai-openai-spring-boot-starter</font>`<font style="color:rgb(53, 56, 65);"> 即可。</font>

```plain
<dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-openai-spring-boot-starter</artifactId>
        </dependency>
```

2）<font style="color:rgb(53, 56, 65);">配置 </font>`<font style="color:rgb(53, 56, 65);">application.yml</font>`

```plain
spring:                                                            
  application:                                                     
    name: higress-demo                                             
                                                                   
  ai:                                                              
    openai:                                                        
      api-key: cbfda275-56ff-4d45-a984-6482502e2f25   #Higress的消费者认证key              
      base-url: http://192.168.65.185:8080/v1    #Higress的网关地址                  
      chat:                                                        
        options:                                                   
          model: deepseek-chat                                     
                                                                   
                                                                   
```

3)<font style="color:rgb(53, 56, 65);"> 使用ChatModel 实现对话</font>

```plain
@RestController
public class ChatModelController {
  private final ChatModel chatModel;

  public ChatModelController(ChatModel chatModel) {
    this.chatModel = chatModel;
  }

  @RequestMapping("/chat")
  public String chat(String input) {
    ChatResponse response = chatModel.call(new Prompt(input));
    return response.getResult().getOutput().toString();
  }
}
```

4）测试

![1741765469155-163d5d69-d4e7-44dd-bfbd-469f7ed6dda1.png](./img/0L-CuNkt1mXzFGRf/1741765469155-163d5d69-d4e7-44dd-bfbd-469f7ed6dda1-085406.png)



> 更新: 2025-04-07 17:02:39  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/lca70bns0cpw7sgl>