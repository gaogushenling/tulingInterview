# Sentinel 与Hystrix的区别是什么

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Hystrix和Sentinel都是微服务架构中实现熔断和限流的工具，它们有以下区别和特点：</font>

**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Hystrix是Netflix开源的熔断器实现，主要用于保护分布式系统中的服务调用</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">。它的主要特点包括线程隔离、资源保护和降级处理。Hystrix通过将每个服务调用放入独立的线程池中来实现线程隔离，防止一个服务的延迟或故障影响其他服务。它通过监控服务调用的成功率、延迟等指标来保护后端资源，并在失败的请求或响应超时达到一定阈值时自动打开熔断器，避免连锁故障。此外，Hystrix还提供了降级处理功能，可以在服务不可用或响应过慢时返回预定义的降级响应，保证系统的可用性。</font>

**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Sentinel是阿里巴巴开源的流量控制和系统保护工具，主要用于实现微服务架构中的流量控制、熔断、降级和系统负载保护等</font>**<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">。它的主要特点包括实时监控和动态规则配置、丰富的流量控制策略、细粒度的服务保护以及支持多种编程语言。Sentinel可以实时监控服务的请求流量和各项指标，并提供实时的仪表盘和可视化的监控界面。它还支持通过API动态配置流量控制和熔断规则，可以根据实际情况进行动态调整。Sentinel提供了多种流量控制策略，包括基于QPS、线程数、并发连接数等多种指标进行的流量控制。此外，Sentinel还支持对每个具体的服务接口进行熔断、降级和限流等操作，以实现精确的服务保护策略。同时，Sentinel不仅支持Java，还支持Go、Python等多种编程语言，使其适用于跨语言的微服务架构。</font>

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">总的来说，Hystrix注重线程隔离和资源保护，适用于保护单个服务调用。而Sentinel注重流量控制和动态规则配置，适用于对整个系统的流量进行监控和保护。根据实际需求和技术栈，可以选择适合的工具来实现微服务架构中的熔断和限流功能。</font>





> 更新: 2023-09-11 14:15:03  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/mwr22wwg7p40zbfi>