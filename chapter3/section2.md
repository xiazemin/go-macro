## 什么是 Micro



Micro 是一个着眼于分布式系统开发的微服务生态系统。

* Micro 是
  [框架](https://github.com/micro/go-micro)
* Micro 也是
  [工具集](https://github.com/micro/micro)
* Micro 有
  [社区](http://slack.micro.mu/)
* Micro 还是一个
  [生态系统](https://micro.mu/explore/)

### Micro是开源的

Micro由开源的库与工具组成，旨在辅助微服务开发。

* **go-micro**
   - 基于Go语言的可插拔RPC微服务开发框架；包含服务发现、RPC客户/服务端、广播/订阅机制等等。
* **go-plugins**
   - go-micro的插件有etcd、kubernetes、nats、rabbitmq、grpc等等。
* **micro**
   - 微服务工具集包含传统的入口点（entry point）；API 网关、CLI、Slack Bot、代理及Web UI。

还有其它相关的库和服务可以参考 [github.com/micro](https://github.com/micro)。

### 社区

我们有上千人的社区在Slack平台上：[slack.micro.mu](http://slack.micro.mu/)；

### 生态系统

Micro跨过了单一组合开发的模式，她的开源工具与服务都是由社区贡献的。

相关的生态系统可以进右边的传送门查看：[micro.mu/explore/](https://micro.mu/explore/).

## 该从何入手

可以先从[go-micro](https://github.com/micro/go-micro)入手。 readme文档里提供了一个demo微服务示例。

如果要了解更多的资源可以查阅[开始入门](https://micro.mu/docs/writing-a-go-service.html) ，或者签出[示例](https://github.com/micro/examples)源码。

可以通过[micro](https://github.com/micro/micro)工具集的cli，web ui，slack，或者api网关（api gateway）来访问操控务。

## micro有哪些使用者

[相关用户](https://micro.mu/docs/users.html)页面列出了一些使用micro服务的公司（列表更新可能不会太及时）。

还有很多公司在使用我们的框架但是没有公开列出来，如果你们你们正在使用micro，可以告诉我们，我们会列出来。

## 如何才能使用micro

其实非常简单

1. 使用
   [go-micro](https://github.com/micro/go-micro)
   编写服务。
2. 使用
   [micro](https://github.com/micro/micro)
   工具集来访问这些服务。

你可以签出我们在github上的简易[Greeter](https://github.com/micro/examples/tree/master/greeter)示例。

  


