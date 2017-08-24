# 动态请求路由

动态请求路由是 linkerd 更为强大和灵活的功能之一。当 linkerd 接收到请求时，它必须以某种方式确定路由该请求到哪里。它通过为请求分配服务名称，然后应用 [dtab](../advanced/dtabs.md) 重写来实现。

这引入了服务目的地（例如，foo服务）和具体目的地（例如在东海岸数据中心运行的foo服务的staging版本）之间的区别。当应用程序只用服务名称来定位请求时，它们才能完全与环境无关。

## 流量转移

通过修改 dtab，我们可以调整从服务名称到具体名称的映射。这允许您将流量从 staging 转移到 prod，从一个服务版本到另一个版本，或从一个数据中心转移到另一个。这些更改可以应用于一定百分比的流量，允许您以增量和受控的方式转移流量。这种流量转移可以实现蓝绿色部署，金丝雀和跨DC故障切换。

使用 [namerd](../advanced/namerd.md) 能够在运行时进行这些 dtab 更改，而无需重新启动 linkerd。

## 每请求路由

可以在每个请求的基础上指定额外的 dtab 规则，仅适用于该请求。`l5d-dtab` HTTP 头中的任何 dtab 规则将附加到用于路由该请求的 dtab。由于稍后的规则具有较高的优先级，因此允许您覆盖请求的目的地。

如果您的应用程序转发 [recommended HTTP headers](https://linkerd.io/config/1.1.3/linkerd#http-headers)，额外的 dtab 规则将随请求传播。这允许您测试服务的分段版本（即使在应用程序拓扑中较深的服务），而不影响生产流量。

有关 linkerd 读取的特殊 header 的更多信息，请参阅 [HTTP header 文档](https://linkerd.io/config/1.1.3/linkerd#http-headers)。有关 linkerd 如何路由请求的更详细的描述，请参阅 [深入的路由文档](../advanced/routing.md)。
