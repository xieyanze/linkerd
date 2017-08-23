# TLS

linkerd 的常见部署模型是以 [linker 到 linker 模式](..advanced/deployment.md) 运行，这意味着在每个网络调用的发送端和接收端都有一个 linkerd。在此模式下，linkerd 可以无缝地升级连接, 将 TLS 添加到所有服务到服务调用。通过在 linkerd 而不是应用程序中处理 TLS，可以加密主机之间的通信，而不需要修改应用程序代码。

要在 linker 到 linker 模式下部署linkerd并启用TLS，我们必须将其配置为在发送请求时使用客户端TLS，并在接收到请求时使用服务器端TLS，这两者都在下面讲述。

## 客户端TLS

为了使 linkerd 发送带有TLS的请求，必须在配置 linkerd 时设置客户端TLS配置参数。 客户端TLS有三种支持的类型：

- [静态TLS](https://linkerd.io/config/1.1.3/linkerd#static-tls)：linkerd 对所有TLS请求使用单个通用名称。这将假定所有该 linkerd 连接到的远程服务器使用相同的TLS证书(或所有使用的证书是用相同公用名称生成的）。

- [带有绑定路径的TLS](https://linkerd.io/config/1.1.3/linkerd#tls-with-bound-path)：linkerd 根据作为出站请求的一部分而发送的路由信息确定远程服务器的通用名称。这允许 linkerd 根据所连接的目标服务使用不同的证书。

- [无验证TLS](https://linkerd.io/config/1.1.3/linkerd#no-validation-tls)：在建立TLS连接之前，linkerd 不验证远程服务器的名称。这将导致不可靠连接，是不安全的。不建议使用此配置。

## 服务器端TLS

为了使 linkerd 用TLS接收请求，必须在配置 linkerd 时设置 [服务器TLS配置参数](https://linkerd.io/config/1.1.3/linkerd#server-tls)。与客户端TLS不同，配置服务器TLS只有一个选项，而它需要提供TLS证书和 linkerd 用于服务入站TLS请求的密钥文件。

## 更多信息

如果您想了解有关在您的环境中设置TLS的更多信息，请查看 Booyant 的关于该主题的 [Transparent TLS with linkerd](https://blog.buoyant.io/2016/03/24/transparent-tls-with-linkerd/?__hstc=249056664.3c6b78fb9cb62c68eaaac6558454a06e.1501146055259.1503396789843.1503452724219.31&__hssc=249056664.3.1503452724219&__hsfp=4035021484) 博客文档，这将提供有用的演练。如果您在 Kubernetes 中运行 linkerd 作为 [服务网格](https://blog.buoyant.io/2016/10/04/a-service-mesh-for-kubernetes-part-i-top-line-service-metrics/?__hstc=249056664.3c6b78fb9cb62c68eaaac6558454a06e.1501146055259.1503396789843.1503452724219.31&__hssc=249056664.3.1503452724219&__hsfp=4035021484)，则设置TLS更加容易; 请参阅在服务网格系列中 [Encrypting all the things](https://blog.buoyant.io/2016/10/24/a-service-mesh-for-kubernetes-part-iii-encrypting-all-the-things/?__hstc=249056664.3c6b78fb9cb62c68eaaac6558454a06e.1501146055259.1503396789843.1503452724219.31&__hssc=249056664.3.1503452724219&__hsfp=4035021484) 博客文章。





