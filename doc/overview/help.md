# 获取帮助

我们很乐意帮助您让您的用例可以用 linkerd 工作，并且我们对您的反馈很感兴趣！

如果您遇到问题或认为您发现bug，请先查看 [常见问题](faq.md)。如果您仍然需要帮助，您可以通过几种方式与我们联系：

- 在 [linkerd Discourse instance](https://discourse.linkerd.io/) 提出有关配置或故障排除的问题。
- 在 [github issues](https://github.com/linkerd/linkerd/issues/new)中提交bug或功能请求。
- 在我们的 [linkerd public Slack](http://slack.linkerd.io/) 中聊天。
- 发送电子邮件至 support@buoyant.io

您还可以在 [linkerd Github repo](https://github.com/linkerd/linkerd) 上报告错误或发起功能请求。

## 远程诊断

诊断为什么 linkerd 有问题是棘手的。你可以通过提供一些东西来帮助我们。

首先，metrics dump 对于我们理解 linkerd 正在做什么是非常关键的。您可以通过运行以下脚本来完成此操作：

```bash
#!/bin/bash

while true; do
  curl -s http://localhost:9990/admin/metrics.json > l5d_metrics_`date -u +'%s'`.json
  sleep 60
done
```

这个脚本会每分钟产生一个文件。

如果这些指标不足，我们也可能会要求您捕获一些网络流量。一种方法是使用tcpdump：

```bash
$ /usr/sbin/tcpdump -s 65535 'tcp port 4140' -w linkerd.pcap
```

（假设您在默认端口 `4140` 运行linkerd）。

在发生问题时运行此命令，并将结果汇总到 tar 或 zip 文件中。您可以直接将这些文件附加到 Github issue。
