# 用Docker运行

如果您使用Docker来运行linkerd，那么就不需要从GitHub中pull如上一节所述二进制发行包。取而代之的是，Buoyant为您提供以下公共Docker镜像：

- [BUOYANTIO/LINKERD:1.1.3](https://hub.docker.com/r/buoyantio/linkerd/)
- [BUOYANTIO/NAMERD:1.1.3](https://hub.docker.com/r/buoyantio/namerd/)

## 标签

这两个库都具有每个镜像的所有稳定版本的标签。要查看发行列表和相关改动，请访问 [linkerd GitHub release](https://github.com/linkerd/linkerd/releases) 页面。

除了版本化的标签外，“latest”标签总是指向最新的稳定版本。对于希望在不手动修改依赖版本的情况下获取新代码的环境是非常有用的，但请注意，latest标签可能拉取一个和上一个版本有突破性变化的镜像,这取决于linkerd发行版本的性质。

此外，“nightly”标签用于从 [linkerd GitHub库](https://github.com/linkerd/linkerd) 中的master分支上的最新提交中提供linkerd和namerd的夜间构建。此镜像不稳定，但可用于测试最近添加的功能和修复。

## 运行

linerd映像的默认入口点运行linkerd可执行文件，这要求在命令行中传递一个linkerd配置文件。最简单的方法是在启动时将配置文件挂载到容器。

例如，给出以下配置，只需将端口8080上接收的http请求转发到在端口9990上运行的linkerd admin服务：

```bash
admin:
  port: 9990

routers:
- protocol: http
  dtab: /svc => /$/inet/127.1/9990;
  servers:
  - port: 8080
```

我们可以使用以下命令启动linkerd容器：

```bash
$ docker run --name linkerd -v `pwd`/config.yaml:/config.yaml buoyantio/linkerd:1.1.3 /config.yaml
...
I 0922 02:01:12.862 THREAD1: serving http admin on /0.0.0.0:9990
I 0922 02:01:12.875 THREAD1: serving http on localhost/127.0.0.1:8080
I 0922 02:01:12.890 THREAD1: linkerd initialized.
```

## 确保它工作

为了验证它是否正常工作，我们可以在运行中的容器中执行exe并通过http路由器配置的端口用curl访问linkerd的admin ping端点：

```bash
$ docker exec linkerd curl -s 127.1:8080/admin/ping
pong
```

成功！ 有关linkerd管理功能的更多信息，请参阅 [“管理”](admin.md) 页面。
