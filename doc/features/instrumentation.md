# 仪器仪表

Linkerd 提供了通信延迟和有效载荷大小的详细直方图以及成功率和负载均衡统计信息，以人类可读和机器可解析的格式。这意味着即使多语言应用程序也可以具有应用程序性能的一致的，全局的视图。有数百个计数器，仪表和直方图可用，包括：

- 延迟 (平均, 最小, 最大, p99, p999, p9999)
- 请求量
- 有效载荷大小
- 成功，重试和失败计数
- 故障分类
- 堆内存和 GC 性能
- 负载均衡统计

虽然 linkerd 提供了一个开放的遥测插件界面，用于与任何 metrics 聚合器集成，但它包括一些开箱即用的常见格式，包括 TwitterServer，Prometheus 和 InfluxDB。

## 进一步阅读

要配置遥测如 Prometheus，请参阅 [linkerd配置的遥测部分](https://linkerd.io/config/1.1.3/linkerd#telemetry)。

有关 metrics 仪器仪表的更多详细信息，请参阅 [入门指南的度量标准部分](../getting-started/admin.md/#metrics)。

为了配置您的 metrics 端点，请参阅 [linkerd配置的管理部分](https://linkerd.io/config/1.1.3/linkerd#administrative-interface)。

关于在 Kubernetes 建立端到端监控管道的指南，请参阅 [ A Service Mesh for Kubernetes, Part I: Top-Line Service Metrics](https://blog.buoyant.io/2016/10/04/a-service-mesh-for-kubernetes-part-i-top-line-service-metrics/)。对于 DC/OS，请参阅 [linkerd on DC/OS for Service Discovery and Visibility](https://blog.buoyant.io/2016/10/10/linkerd-on-dcos-for-service-discovery-and-visibility/) 。这两个都利用了我们的开箱即用的 linkerd + prometheus + grafana 构建，[linkerd-viz](https://github.com/linkerd/linkerd-viz)。
