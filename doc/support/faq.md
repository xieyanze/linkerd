# 常见问题

### “linkerd” 如何发音?

“linker-DEE”.

### 为什么称为 linkerd?

linkerd 可以被认为是微服务的动态链接器。在操作系统中，动态链接器获取有关要执行的库和函数调用的名称的运行时信息，并执行使该函数可调用到可执行文件所需的任何工作。linkerd 对微服务进行类似的任务：接受服务名称和对该服务（HTTP，gRPC等）的调用，并且执行使调用成功所需的工作，包括路由，负载均衡，和重试。

### 如何访问linkerd仪表板（或“管理”）页面？

默认情况下，可以通过 `http://localhost:9990` 访问管理页面。 您可以在 [linkerd配置的admin部分](https://linkerd.io/config/1.1.3/linkerd#administrative-interface) 指定不同端口。

### 如何获得上游服务metrics？

linkerd 以 JSON 格式在 `http://localhost:9990/admin/metrics.json` 上暴露机器可读的metrics。这是可配置的 - 见上文。

### linkerd 的日志去哪里了？

linkerd 日志输出到stderr。对于 HTTP 路由，可以通过 [配置文件中的 `httpAccessLog` 键](https://linkerd.io/config/1.1.3/linkerd#http-1-1-protocol) 配置额外的访问日志。

### linkerd 是否支持动态配置重新加载？

不，我们更喜欢避免这种模式，并将可变的东西卸载到单独的服务。例如，linkerd 使用服务发现来处理已部署实例的变更，并使用namerd来处理路由策略的变更。

### linkerd 如何处理服务错误？

linkerd不加修改的传递下游服务生成的错误。

对于HTTP，使用 `Content-type` `text/plain` 提供错误，并在应答正文和 `l5d-err` header中都有解释性的错误消息。

当配置的 namer 无法解析服务来处理请求时，linkerd 响应 `400 Bad Request`。 这可能是由于 linkerd 配置错误，服务发现问题或附加到请求的不正确的 `l5d-dtab` header引起的。

与下游服务（包括超时，无法建立连接等）通信的所有其他故障都表示为 `503 Bad Gateway`。

### 为什么我看到“No hosts available”错误？

如果您在 linkerd 日志或响应中看到““No hosts available”错误消息，则表示 linkerd 无法将请求的服务名称转换为物理目标。这可能是因为dtab不正确，服务发现查找错误，或者目标服务未运行。

要调试此错误，可以使用 linkerd 管理仪表板中的“dtab playground”。只需在浏览器中访问`<linkerd host>:9990/delegator`。此UI将向您显示 dtab 和 namers 如何转换请求的名称的每个步骤。

（有关 linkerd 如何处理名称的更多信息，请参阅 [路由](../advanced/routing.md) 页面。）

