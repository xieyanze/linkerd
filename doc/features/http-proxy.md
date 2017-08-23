# HTTP代理集成

几乎所有 HTTP 客户端都支持通过中间代理进行调用。传统上，这被用于在与外部世界的连接受到限制的防火墙环境中运行。然而，由于 linkerdd 可以作为 HTTP 代理，并且由于使用代理通常可以在没有代码更改的情况下实现，所以这种方法为使用 HTTP 的应用程序提供了一个简单的集成路径。

## 用linkerd作HTTP代理

linkerd 可以作为 HTTP 代理工作，无需任何额外的配置，提供关键功能，如[负载均衡](load-balancing.md)，[服务发现](service-discovery.md)和[动态请求路由](routing.md)，无需额外的费用。默认情况下，linkerd 会根据作为请求的一部分发送的 HTTP host 路由 HTTP 请求。例如，假设 linkerd 在端口 4140 上本地运行，并且已配置为将请求路由到在其他地方运行的“hello”服务的实例。在这种场景下，可以利用 linkerd 的代理集成，并使用以下方式向“hello”服务发出 curl 请求：

```bash
$ http_proxy=localhost:4140 curl http://hello/
```

通过设置 `http_proxy` 变量，curl 将直接发送代理请求到 linkerd，而不在 DNS 中实际查找“hello”。linkerd 依次在每个被配置为用于服务发现的后端查找“hello”服务，并且它将相应地路由请求。

Using an HTTP proxy
Configuring your application to use an HTTP proxy can often be done without code changes. However, the specifics of this configuration are language-dependent. Here are some examples for common setups:

## 使用HTTP代理

配置应用程序使用 HTTP 代理通常可以在不修改代码的情况下完成。但是，这种配置的细节是依赖于语言的。以下是常见设置的一些示例：

- **C，Go，Python，Ruby，Perl，PHP，curl，wget，Node.js** 的请求包：这些语言和工具支持 `http_proxy` 环境变量，它在所有请求中设置全局 HTTP 代理。简单地设置这个变量就好了。

- **Java，Scala，Clojure和JVM语言**：可以通过设置 `http.proxyHost` 和 `http.proxyPort` 环境变量（例如，`java -Dhttp.proxyHost=127.0.0.1 -Dhttp.proxyPort=4140`）将 JVM 配置为使用 HTTP 代理...）。

- **Node.js** 对于Node而言，使用全局代理没有方便的无代码修改的方法。选项包括 [请求](https://www.npmjs.com/package/request) 或 [全局隧道](https://www.npmjs.com/package/global-tunnel) 包。

## 备选方案

原则上，linkerd 可以在请求的任何组件上进行路由, 从进入端口到有效载荷内容。实际上，对于 HTTP 调用，在 host 上路由（使用默认 methodAndHost 标识符）或 URL 路径（使用路径标识符）是最自然的。

如果全局 HTTP 代理方法不可行，那么在连接到 linkerd 时任何允许设置 host 或 URL路径的机制也将起作用。例如，代理设置通常可以根据每个请求直接在 HTTP 客户端中进行配置。或者，直接连接到 linkerd，并将显式的 `Host：` header设置为请求的一部分将允许 linkerd 在这个 host 上做路由,如同代理方式一样。

如果你在 Kubernetes 上运行, 另一种选择是通过 iptables 规则使用 [透明代理](transparent-proxying.md)。

