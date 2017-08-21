# 管理

此页面介绍了 linkerd 管理界面中包含的一些主要功能。有关可用端点的完整列表，请参阅 [配置文档](https://linkerd.io/config/1.1.3/linkerd#administrative-interface)。

## Dashboard

linkerd 在端口 9990 上运行管理 Web 界面。如果在本地运行了 linkerd，只需访问 `http://localhost:9990/` 即可查看。 Dashboard 显示所有已配置路由器的请求量，成功率，连接信息和延迟指标，以及所有被 linkerd 动态路由请求的客户端。图表实时更新，因此您可以立即感受到您的服务的健康状况。

## Metrics

linkerd 还以多种格式发布其 metrics 的机器可读版本。这些 metrics 旨在由外部metrics收集工具进行轮询，并发送到后端，如Prometheus，InfluxDB和StatsD。

所有收集的指标都可以使用JSON，使用 `/admin/metrics.json` 端点。例如，如果您在本地运行 linkerd，则可以运行：

```bash
$ curl -s http://localhost:9990/admin/metrics.json?pretty=1 | head -n4
{
  "clnt/zipkin-tracer/available" : 1.0,
  "clnt/zipkin-tracer/cancelled_connects" : 0,
  "clnt/zipkin-tracer/closes" : 294,
  ...
```

请注意，pretty=1 参数仅仅是格式化需要。

要启用其他 metrics 端点，例如Prometheus，InfluxDB或StatsD，请查看 [linkerd配置的“Telemetry/遥测”部分](https://linkerd.io/config/1.1.3/linkerd#telemetry)。

### Prometheus

linkerd 提供了一个 metrics 端点 `/admin/metrics/prometheus`，专门用于将统计信息导出到 Prometheus。 要启用 Prometheus 遥测仪，请将其添加到您的 linkerd 配置中：

```bash
telemetry:
- kind: io.l5d.prometheus
```

您可以通过使用该端点作为 Prometheus scrape 配置的一部分，将 Prometheus 配置为自动从您的 linkerd 实例收集统计信息。例如：

```bash
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'linkerd'
    metrics_path: /admin/metrics/prometheus
    static_configs:
    - targets:
      - '1.2.3.4:9990'
      - '2.3.4.5:9990'
      - '3.4.5.6:9990'
```

该配置将从三个独立的 linkerd 实例中抓取 metrics。

### InfluxDB

linkerd 提供了一个 metrics 端点 `/admin/metrics/influxdb`，专门用于在InfluxDB LINE协议中导出统计信息。您可以配置 Telegraf 自动从您的 linkerd 实例收集统计信息。 通过 [linkerd-examples repo 的 InfluxDB 部分](https://github.com/linkerd/linkerd-examples/tree/master/influxdb) 来看一个完整示例。

### StatsD

linkerd支持将 metrics 推送到 StatsD 后端。只需将一个 StatsD 配置块添加到您的 linkerd 配置中即可：

```bash
telemetry:
- kind: io.l5d.statsd
  experimental: true
  prefix: linkerd
  hostname: 127.0.0.1
  port: 8125
  gaugeIntervalMs: 10000
  sampleRate: 0.01
```

## Dtab playground

管理界面还提供了一个 Web UI，您可以使用它来帮助您调试在正在运行的 linkerd 实例上设置的 Dtab 规则。这提供有关 linkerd 将如何路由您的请求的有价值的见解。该用户界面可在 `/delegator` 访问，在配置的管理端口上。

## 关闭

您可以通过向 `/admin/shutown` 发送POST请求来优雅关闭 linkerd，例如：

```bash
curl -X POST http://localhost:9990/admin/shutdown
```
