# 用kubernetes运行

如果您有一个Kubernetes集群，或者仅仅是运行[Minikube][]，则将Linkerd部署为服务网格是开始的最快方式。它不仅部署非常简单，还适用于大多数生产用例，提供服务发现，仪器仪表，智能客户端负载均衡，熔断器和开箱即用的动态路由。

Linkerd服务网格被部署为Kubernetes [DaemonSet][]，在集群的每个节点上运行一个Linkerd pod。在Kubernetes中运行的应用程序可以通过在其节点上运行的Linkerd发送他们所有网络流量来利用服务网格。

## 部署Linkerd服务网格

使用以下命令部署Linkerd服务网格：

```bash
kubectl create ns linkerd
kubectl apply -f https://raw.githubusercontent.com/linkerd/linkerd-examples/master/k8s-daemonset/k8s/servicemesh.yml
```

您可以验证Linkerd是否已成功部署,运行:

```bash
kubectl -n linkerd port-forward $(kubectl -n linkerd get pod -l app=l5d -o jsonpath='{.items[0].metadata.name}') 9990 &
```

然后通过在浏览器中访问 `http://localhost:9990` 来查看Linkerd管理控制面板。

Note that if your cluster uses CNI, you will need to make a few small changes to the Linkerd config to enable CNI compatibility. These are indicated as comments in the config file itself. You can learn more about CNI compatibility on our Flavors of Kubernetes page.

请注意，如果您的群集使用CNI，则需要对Linkerd配置进行一些小的更改才能开启CNI兼容性。这些在配置文件本身表示为注释。您可以在我们的 [Flavors of Kubernetes page][Flavors] 页面上了解有关CNI兼容性的更多信息。

## 配置应用程序

要将应用程序配置为使用Linkerd进行HTTP流量，您可以将 `http_proxy` 环境变量设置为 `$(NODE_NAME):4140`,这里 `NODE_NAME` 是运行应用程序实例的节点的名称。`NODE_NAME` 环境变量可以在实例的pod规范中通过使用 [Kubernetes downward API][downward] 设置：

```bash
        env:
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: http_proxy
          value: $(NODE_NAME):4140
```

请参阅我们的 [hello world][] Kubernetes配置,作为完整的例子。

请注意，`spec.nodeName` 在某些环境（如[Minikube][]）中不起作用。请参阅我们的 [Flavors of Kubernetes page][Flavors] 了解解决方法。

如果您的应用程序不支持 `http_proxy` 环境变量，或者要将应用程序配置为使用Linkerd进行 HTTP/2或gRPC流量，则必须配置应用程序直接将流量发送到Linkerd：

- $(NODE_NAME):4140 用于 HTTP
- $(NODE_NAME):4240 用于 HTTP/2
- $(NODE_NAME):4340 用于 gRPC

如果您直接向Linkerd发送 HTTP 或 HTTP/2 请求，则必须将 Host/Authority 标头设置为 `<service>` 或 `<service>.<namespace>`.其中 `<service>` 和 `<namespace>` 是想要代理的服务和命名空间的名称。如果没有指定，则 `<namespace>` 默认为 `default`。

如果您的应用程序接收 HTTP，HTTP/2 和/或 gRPC 请求，则它必须有Kubernetes服务对象,分别具有名为 `http`，`h2` 和/或 `grpc` 的端口的。

## Ingress

Linkerd服务网格也可配置为作为Ingress控制器工作。只需创建一个Ingress资源,定义好需要的路由器,然后发送请求到群集的入口地址的80端口（或HTTP/2的端口8080）。在具有外部负载均衡器的云环境中，入口地址是外部负载均衡器的地址。除此以外，可以将任何节点的地址用作入口地址。

有关更多详情，请参阅我们的 [Ingress博文][Ingress]。

## 下一步

这个配置是一个很好的起点，适用于广泛的用例。请查看我们的 [Kubernetes服务网格博客系列][blog]，了解如何启用更高级的Linkerd功能，如 [服务到服务加密][encyption]，[持续部署][continuous] 和 [每请求路由][routing]。


[Minikube]:https://github.com/kubernetes/minikube
[DaemonSet]:https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[Flavors]:https://discourse.linkerd.io/t/flavors-of-kubernetes/53
[downward]:https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/
[hello world]:https://github.com/linkerd/linkerd-examples/blob/master/k8s-daemonset/k8s/hello-world.yml
[Ingress]:https://buoyant.io/2017/04/06/a-service-mesh-for-kubernetes-part-viii-linkerd-as-an-ingress-controller/
[blog]:https://buoyant.io/2016/10/04/a-service-mesh-for-kubernetes-part-i-top-line-service-metrics/
[encyption]:https://buoyant.io/2016/10/04/a-service-mesh-for-kubernetes-part-i-top-line-service-metrics/
[continuous]:https://buoyant.io/2016/11/04/a-service-mesh-for-kubernetes-part-iv-continuous-deployment-via-traffic-shifting/
[routing]:https://buoyant.io/2017/01/06/a-service-mesh-for-kubernetes-part-vi-staging-microservices-without-the-tears/
