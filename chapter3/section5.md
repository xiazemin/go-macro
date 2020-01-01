## Micro与Go-Kit比较

Go-kit声称自己是一个微服务的标准库。像GO一样，go-kit提供独立的包，通过这些包，开发者可以用来组建自己的应用程序。Go-kit非常不错，基于Go-kit，你可以完全掌控你定义的服务。

Go-micro则是一个面向微服务的可插拔RPC框架。go-micro是一个只在特殊方向上努力的框架，它尝试简化分布式系统之间的通信，所以我们可以花更多的时间在我们需要关注的业务逻辑上。对于想快速启动，把程序跑起来，同时用拥有一些可插拔的能力从基础架构中断开的能力，而不用修改代码，那么go-micro也很不错。

Micro作为一个微服务工具库，好比一把瑞士军刀，在我们构建微服务时，可以提供传统的接入点，比如http api gateway，web ui，cli，slack bot等等。Micro使用工具来引导架构关注点之间逻辑上的隔离，推动开发者创建API层的服务来暴露对外的API接口，并且创建隔离于对外API的Web层微服务。

如果想全盘掌控，那么使用go-kit；但是如果想弄一个有想法框架，使用go-micro。

## 想了解更多

* slack社区 - 
  [slack.micro.mu](http://slack.micro.mu/)
* 博客 - 
  [micro.mu/blog](https://micro.mu/blog)
* 联系我们 - 
  [contact@micro.mu](mailto:contact@micro.mu)



