# 用istio运行

[Istio][] 是连接，管理和保护微服务的开放平台。Linkerd 用于云原生应用程序的开源服务网格。Istio 和 Linkerd 可以一起工作，Istio充当控制平台, 跨越 Linkerd 实例。

Linkerd的Istio集成是实验性的，目前支持[路由规则][]，[入口][]，[出口][]和[度量][]。对[故障注入][]，[目的地策略][]，[路由策略][]，[ACL][] 和 [auth][] 的支持即将推出。

## 安装Istio

Istio + Linkerd由5个主要组成部分组成：

- Istio Pilot 向服务网格提供路由规则，策略和服务发现信息。
- Istio Mixer 从服务网格中获取指标，并将其传递给后端，如Prometheus。
- Linkerd 服务网格代理所有的服务间流量。
- Linkerd Ingress，作为 [ingress controller][] 提供服务的Linkerd。
- Linkerd Egress，处理从集群发出的所有流量的Linkerd。

Linkerd 目前支持 Istio 0.1.6。要安装Istio，请按照下列步骤操作：

1. 按照 [Istio安装指南][] 中的步骤1-4安装istioctl二进制包，并在必要时记录集群的RBAC。
2. 运行 `kubectl apply -f https://raw.githubusercontent.com/linkerd/linkerd-examples/master/istio/istio-linkerd.yml` ,这将安装：

    - Istio Pilot
    - Istio Mixer
    - Linkerd Service Mesh
    - Linkerd Ingress
    - Linkerd Egress

3.（可选）按照[Istio安装指南][]中的步骤启用 metrics 收集。

## 部署应用程序

为了使您的应用程序使用Linkerd服务网格，您可以使用名为 `istio-init` 的 [init container][] 进行部署。 此 init container 配置 iptables 规则以通过Linkerd服务网格透明地重定向所有传出的请求。

为了使用 init container 轻松部署应用程序，可以安装 `linkerd-inject` 工具:

```bash
go get github.com/linkerd/linkerd-inject
```

然后使用它来部署你的应用程序:

```bash
kubectl apply -f <(linkerd-inject -f samples/apps/bookinfo/bookinfo.yaml)
```

（Minikube用户在使用 `linkerd-inject` 时需要使用 `-useServiceVip` 标志 ）

或者，您可以将 init container 手动添加到您的Kubernetes配置，而不使用 `linkerd-inject`：

```bash
initContainers:
- name: init-linkerd
  image: linkerd/istio-init:v1
  env:
  - name: NODE_NAME
    valueFrom:
      fieldRef:
        fieldPath: spec.nodeName
  args:
    - -p
    - "4140" # port of the Daemonset linkerd's incoming router
    - -s
    - "L5D" # linkerd Daemonset service name, uppercased
    - -m
    - "false" # set to true if running in minikube
  imagePullPolicy: IfNotPresent
  securityContext:
    capabilities:
      add:
      - NET_ADMIN
```

## 试试看

尝试实战 Istio [Request Routing Demo][] 以查看所有情况。请注意，要部署 BookInfo 示例，您需要使用linkerd-inject 命令：

```bash
kubectl apply -f <(linkerd-inject -f samples/apps/bookinfo/bookinfo.yaml)
```

而不是 `istioctl kube-inject` 命令：

```bash
kubectl apply -f <(istioctl kube-inject -f samples/apps/bookinfo/bookinfo.yaml)
```

有关 Istio + Linkerd 集成中特殊 Istio 功能的更多信息，请继续阅读。

## Metrics

如果还没有这样做，请安装指 Metrics 集组件：

```bash
kubectl apply -f install/kubernetes/addons/prometheus.yaml
kubectl apply -f install/kubernetes/addons/grafana.yaml
kubectl apply -f install/kubernetes/addons/servicegraph.yaml
```

您现在可以按照 [Istio文档][] 中的描述的那样查看 Grafana dashboard 。

请注意，一些指标可能会空缺，直到 Linkerd 添加对它们的支持。

## Ingress

Ingress可以使用Ingress资源进行配置，如 [Istio Ingress 指南][] 中所述。请注意，要启用HTTPS，您必须按照 [Linkerd Ingress博客文章][] 而不是 Ingress 资源中所述在入口 linkerd 配置中启用TLS 。

## Egress

具有与集群中任何服务不匹配的 Host/Authority header的所有请求都将发送到专用的出口Linkerd。然后，出口Linkerd将请求转发到集群外的服务（如果指定了端口443，则使用HTTPS）。没有必要创建一个 `ExternalName` 服务资源。有关更多详细信息，请参阅 [Linkerd Egress 博客文章][]。

## 结论

以上部分介绍如何和Istio一起使用Linkerd，包括如何配置 ingress，egress 和 metrics 收集。这种集成目前处于实验状态; Istio正在迅速演进，而我们继续强化继承功能集。

[Istio]:https://istio.io/
[路由规则]:https://istio.io/docs/tasks/request-routing.html
[入口]:https://istio.io/docs/tasks/ingress.html
[出口]:https://istio.io/docs/tasks/egress.html
[度量]:https://istio.io/docs/tasks/metrics-logs.html
[故障注入]:https://github.com/linkerd/linkerd/issues/1407
[目的地策略]:https://github.com/linkerd/linkerd/issues/1406
[路由策略]:https://github.com/linkerd/linkerd/issues/1405
[ACL]:https://github.com/linkerd/linkerd/issues/1403
[auth]:https://github.com/linkerd/linkerd/issues/1409
[ingress controller]:https://kubernetes.io/docs/concepts/services-networking/ingress/
[Istio安装指南]:https://istio.io/docs/tasks/installing-istio.html
[init container]:https://kubernetes.io/docs/concepts/workloads/pods/init-containers/
[Request Routing Demo]:https://istio.io/docs/tasks/request-routing.html
[Istio文档]:https://istio.io/docs/tasks/installing-istio.html#verifying-the-grafana-dashboard
[Istio Ingress 指南]:https://istio.io/docs/tasks/ingress.html
[Linkerd Ingress博客文章]:https://blog.buoyant.io/2017/04/06/a-service-mesh-for-kubernetes-part-viii-linkerd-as-an-ingress-controller/index.html
[Linkerd Egress 博客文章]:https://blog.buoyant.io/2017/06/20/a-service-mesh-for-kubernetes-part-xi-egress/
