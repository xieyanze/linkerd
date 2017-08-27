# Linkerd官方文档中文版

## 介绍

Linkerd 是一个提供弹性云端原生应用服务网格的开源项目。其核心是一个透明代理，可以用它来实现一个专用的基础设施层以提供服务间的通信，进而为软件应用提供服务发现、路由、错误处理以及服务可见性等功能，而无需侵入应用内部本身的实现。

## 内容

本书籍为 Linkerd 官方文档的中文版.

当前内容：

* [官方文档(翻译)](doc/index.md)
    * [概况]()
        * [介绍](doc/overview/index.md)
        * [linkerd是什么?](doc/overview/what-is-linkerd.md)
    * [入门]()
        * [概况](doc/getting-started/index.md)
        * [本地运行](doc/getting-started/locally.md)
        * [用docker运行](doc/getting-started/docker.md)
        * [在kubernetes中运行](doc/getting-started/k8s.md)
        * [在DC/OS中运行](doc/getting-started/dcos.md)
        * [用istio运行](doc/getting-started/istio.md)
        * [在ECS中运行](doc/getting-started/ecs.md)
        * [管理](doc/getting-started/admin.md)
    * [特性]()
        * [概况](doc/features/index.md)
        * [负载均衡](doc/features/load-balancing.md)
        * [熔断](doc/features/circuit-breaking.md)
        * [服务发现](doc/features/service-discovery.md)
        * [动态请求路由](doc/features/routing.md)
        * [重试次数和截止时间](doc/features/retries-deadlines.md)
        * [TLS](doc/features/tls.md)
        * [HTTP代理集成](doc/features/http-proxy.md)
        * [透明代理](doc/features/transparent-proxying.md)
        * [gRPC](doc/features/grpc.md)
        * [分布式跟踪](doc/features/distributed-tracing-and-instrumentation.md)
        * [仪器仪表](doc/features/instrumentation.md)
    * [配置]()
        * [概况](doc/configuration/index.md)
        * [linkerd](https://linkerd.io/config/latest/linkerd)
        * [namerd](https://linkerd.io/config/latest/namerd)
    * [高级]()
        * [概述](doc/advanced/index.md)
        * [路由](doc/advanced/routing.md)
        * [namerd](doc/advanced/namerd.md)
        * [dtabs](doc/advanced/dtabs.md)
        * [部署](doc/advanced/deployment.md)
        * [插件](doc/advanced/plugins.md)
    * [支持]()
        * [常见问题](doc/support/faq.md)
        * [获取帮助](doc/support/help.md)
        * [外部资源](doc/support/external-resources.md)
        * [联系我们](doc/support/contact.md)
    * [企业]()
        * [企业](doc/enterprise/index.md)
* [官方博客](blog/index.md)
	* [超越轮循:为了延迟的负载均衡](blog/beyond-round-robin-load-balancing-for-latency.md)
    * [LINKERD：用于微服务的TWITTER风格可操作性](blog/linkerd-twitter-style-operability-for-microservices.md)

后续计划中的内容：

- 官方网站的博客文章(进行中)
- linkerd 配置文档
- namerd 配置文档

## 访问方式

文档内容发布于 gitbook，请点击下面的链接阅读:

- [在线阅读](https://linkerd.doczh.cn)
- [gitbook书籍首页](https://www.gitbook.com/book/doczhcn/etcd/)：可选择下载 pdf/epub/mobi 格式