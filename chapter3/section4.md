## API、Web、SRV 服务之间的区别是什么

## 

![](//upload-images.jianshu.io/upload_images/7339866-52ada579b5bbea7a.png?imageMogr2/auto-orient/strip|imageView2/2/w/633/format/webp)

image

API、Web、SRV作为micro工具集的一部分，我们尝试着使用一些设计模式规划出弹性的架构，通过把**API接口**、**Web管理控制台**、**SRV**（SRV是service的简称，可理解为后台业务逻辑部分）。

### API 服务

micro api默认把**go.micro.api**作为API网关服务的命名空间，micro api遵从API网关模式。

查看更多内容请求翻阅：[api](https://github.com/micro/micro/tree/master/api)

### Web 服务

Web服务由micro web提供，默认命名空间是**go.micro.web**。我们坚信，web应用作为微服务中的一等公民，所以把构建web管理控制台作为微服务的一部分显得非常重要。micro web其实是一个反向代理，并把http请求基于路径解析到适当的web应用程序。

查看更多内容请求翻阅：[web](https://github.com/micro/micro/tree/master/web)

### SRV 服务

SRV 服务是RPC服务的基础，也就是你常写的服务类型。我们一般把它称作RPC或后后端服务，因为它作为后台架构的一部分，不应该暴露在最外层。默认情况下，我们使用**go.micro.srv**作为它的命名空间，或者你可以使用像**com.example.srv**这样的名字。

## Micro的性能如何

性能目前并不是Micro主要关注的事情。虽然代码写出来应该是最优化并且要避免超过负载，但是确实是没有这么多时间花在基准压力测试上。与net/http或者其它web框架相互比较目前毫无意义。Micro提供的是更高级别的微服务需求，这里面就包含服务发现、负载均衡、消息编码等等。要比较，我想得把这些都一起比较了才行。

如果你还是关心性能问题，最简单的方式就是通过传递以下参数运行程序，便可以提取出最大值。

```
--selector=cache # 激活基于内存的服务节点发现机制
--client_pool_size=10 # 激活客户端连接池


```

## Micro支持gRPC吗

支持。这儿有几个插件：transport、client、server。

可以查看[micro/go-plugins](https://github.com/micro/go-plugins).

我们也提供了golang版本的gRPC快速上手demo：[micro/go-grpc](https://github.com/micro/go-grpc).

  


