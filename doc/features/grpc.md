# gRPC

linkerd 支持配置 gRPC 客户端和服务器，可以将 gRPC 轻松引入应用程序。使用 linkerd 来路由 gRPC 请求可以开启灵活的分布式通信，以及支持由 gRPC 和 Protocol Buffer 提供的结构化数据，双向流，流控制和强大的跨平台客户端库。

## 传输

用于 gRPC 底层传输的是 HTTP/2。linkerd 支持 [配置启用HTTP/2的路由器](https://linkerd.io/config/1.1.3/linkerd#http-2-protocol)，这也可用于路由 gRPC 请求。当 gRPC 客户端发送请求时，它们将路由信息包含在 HTTP/2 的 `path` pseudo-header 中。gRPC 请求的路径前缀为 `/serviceName/methodName` 段，并且可以使用 [Header Path Identifier](https://linkerd.io/config/1.1.3/linkerd#header-path-identifier) 将 linkerd 配置为读取该header的值并相应地路由请求。有关 linkerd 路由请求的更多信息，请参阅 [路由](routing.md) 功能页面。

## 认证

大多数 gRPC 语言实现需要使用 TLS，而 linkerd 支持使用 TLS 配置 gRPC 客户端和服务器，尽管它不是严格要求的。有关设置TLS的更多信息，请参阅 [TLS](tls.md) 功能页面。

## 更多信息

如果您想了解更多关于使用 linkerd 路由 gRPC 请求的信息，请查看关于主题为：[HTTP/2, gRPC and linkerd](https://blog.buoyant.io/2017/01/10/http/2-grpc-and-linkerd/?__hstc=249056664.3c6b78fb9cb62c68eaaac6558454a06e.1501146055259.1503469399889.1503482111539.35&__hssc=249056664.2.1503482111539&__hsfp=4035021484) 的 Buoyant 的博客文章，提供了一个全面的介绍。
