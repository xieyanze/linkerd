# 透明代理

如果您在 Kubnernetes 中运行，则可以使用 [linkerd-inject](https://github.com/linkerd/linkerd-inject) 工具透明地通过 [Daemonset linkerd](https://github.com/linkerd/linkerd-examples/blob/master/k8s-daemonset/k8s/linkerd.yml) 代理请求。该脚本在每个pod中运行一个[initContainer](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，在每个pod上设置 `iptables` 规则，将流量转发到在 node 上运行的linkerd。请注意，此设置将所有出站流量代理到单个 linkerd 端口，因此如果使用多个协议，则不能工作。

使用 `linkerd-inject`:

```bash
# install linkerd-inject
$ go get github.com/linkerd/linkerd-inject

# inject init container and deploy this config
$ kubectl apply -f <(linkerd-inject -f <your k8s config>.yml -linkerdPort 4140)
```

请注意，在 minikube 中，需要使用 `-useServiceVip` 标志。

如果不想使用脚本修改配置，则可以手动将以下 `initContainer` 规范插入到配置中：

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

## 非Kubernetes环境

设置 iptables 规则的 [prepare-proxy.sh](https://github.com/linkerd/linkerd-inject/blob/master/docker/prepare_proxy.sh) 脚本假设您运行在Kubernetes（并且您正在运行 Daemonset linkerd），但是也可以将 `iptables` 规则设置为在其他环境中透明地代理请求。如果您正在每个主机运行一个 linkerd，那么查看该文件中的 `OUTPUT` 链规则可以让您开始。
