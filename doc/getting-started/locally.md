# 本地运行

## 本地运行

本指南将引导您完成在本地下载和运行 linkerd 所需的步骤。

为了在本地运行 linkerd，必须安装有 Java 8。您可以运行以下步骤检查您的Java版本：

```bash
$ java -version
java version "1.8.0_66"
```

linkerd 与 Oracle 和 OpenJDK 兼容。 如果您需要安装 Java 8，您可以下载任何一个。

- [下载 ORACLE JAVA 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [下载 OPENJDK 8](http://openjdk.java.net/install/)

### 下载安装

首先，下载最新的 linkerd 二进制发行包。

- [下载 LINKERD](https://github.com/linkerd/linkerd/releases)

下载发行包后，解压缩：

```bash
$ tar -xzf linkerd-1.1.3.tgz
$ cd linkerd-1.1.3
```

发行包将包含这些文件：

- config/inkerd.yaml - 定义路由，服务器，协议和端口的配置文件
- disco/- 基于文件的服务发现配置
- docs/- 文档
- linkerd-1.1.3-exec - linkerd可执行文件
- logs/ - 写入 linkerd 日志的默认位置

### 运行

解压缩发行包后，可以使用 linkerd-1.1.3-exec 启动和停止 linkerd。

要启动 linkerd，请运行：

```bash
$ ./linkerd-1.1.3-exec config/linkerd.yaml
```

### 确认在工作

您可以通过发送一些 HTTP 流量来验证 linkerd 可以工作。开箱即用，linkerd 配置为侦听端口 4140，并将任何`Host` header 设置为 “web” 的 HTTP 调用路由到端口 9999 上监听的服务。

您可以通过在端口 9999 上运行简单的服务进行测试：

```bash
$ echo 'It works!' > index.html
$ python -m SimpleHTTPServer 9999
```

这将是我们的目标服务器，并将响应任何 HTTP 请求以友好的响应。我们可以通过连接到 linkerd 并指定相应的 host header 来将流量发送到此目的地：

```bash
$ curl -H "Host: web" http://localhost:4140/
It works!
```

因为我们已经要求 linkerd 代理“web” host，所以我们的请求被路由到端口 9999 上的服务器，响应被代理给客户机。跑起来啦！

请注意，如果您不提供与其中一个可路由服务的名称相匹配的Host header，则 linkerd 将请求失败：

```bash
$ curl -I -H "Host: foo" http://localhost:4140/
HTTP/1.1 502 Bad Gateway
```

当然，命名服务比这好多了！ 在下一节中，我们将看到在哪里指定上面使用的服务信息。

## 基于文件的服务发现

在 linkerd 附带的配置下，需要解析服务端点的第一个查找的位置是 `disco/` 目录。 （有关这个简单的 [基于文件的服务发现](https://linkerd.io/config/1.1.3/linkerd#file-based-service-discovery) 系统如何工作的更多信息，请参阅配置指南。）通过此配置，linkerd 将查找与目标的具体名称对应的名称的文件，并且预期这些文件包含以换行符分隔的地址列表，以 `host port` 的形式。

默认配置如下所示：

```bash
$ head disco/*
==> disco/thrift-buffered <==
127.0.0.1 9997

==> disco/thrift-framed <==
127.0.0.1 9998

==> disco/web <==
127.0.0.1 9999
```

正如你所看到的，有一个名为“web”的目的地由单一地址 `127.0.0.1 9999` 支持，以及由`127.0.0.1 9998` 支持的 thrift framed 目的地，以及由`127.0.0.1 9997` 支持的 thrift buffered 目的地。请注意，与所有服务发现端点一样，linkerd 会监视此目录的更改，因此随时可以随时添加，删除和编辑文件，无需重新启动。

linkerd 附带的路由配置非常简单，并直接路由到此目录中指定的具体名称。换句话说，如上所述，向 linkerd  要求“web”服务将导致它连接到 `disco/web` 文件中的一个端点。

此路由配置对于演示基本功能很有帮助，但是 linkerd 具有更多功能，包括多个服务发现端点，每请求路由规则，调试代理注入，服务故障切换等。有关 [linkerd 路由功能](../advanced/routing.md) 的详细信息，请参阅路由页面。
