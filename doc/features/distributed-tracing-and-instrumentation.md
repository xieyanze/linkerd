# 分布式跟踪和仪器仪表

随着服务的数量和复杂性的增加，跨数据中心的统一的可观察性变得越来越重要。Linkerd 的跟踪和度量工具旨在汇总，为所有服务的健康提供广泛而细致的洞察。Linkerd 作为服务网格的角色使其成为可观察性信息的理想数据源，特别是在多语言环境中。

当请求通过多个服务时，使用传统的调试技术来识别性能瓶颈变得越来越困难。分布式跟踪提供通过多个服务的请求的整体视图，允许立即识别延迟问题。

使用 linkerd，分布式跟踪是免费的。只需配置 linkerd 导出跟踪数据到后端跟踪聚合器(如[Zipkin](http://zipkin.io/))。这将显示请求中每次跳跃的延迟，重试和故障信息。

## 进一步阅读

如果您准备好开始使用分布式跟踪，请参阅 [linkerd配置的Tracers部分](https://linkerd.io/config/1.1.3/linkerd#tracers)。

有关设置端到端分布式跟踪管道的指南，请参阅 Buoyant 博客上的 [Distributed Tracing for Polyglot Microservices](https://blog.buoyant.io/2016/05/17/distributed-tracing-for-polyglot-microservices/)。




