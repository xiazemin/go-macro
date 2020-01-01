# go-macro

基于Golang的微服务框架如今有很多，如go-kit、go-micro、kite、gizmo等，每一种都自有其优点。

## 一、go-micro 是什么 {#item-1}

go-micro是基于Go语言实现的插件化RPC微服务框架，与go-kit，kite等微服务框架相比，它具有易上手、部署简单、工具插件化等优点。

go-micro框架提供了服务发现、负载均衡、同步传输、异步通信以及事件驱动等机制，它尝试去简化分布式系统间的通信，让我们可以专注于自身业务逻辑的开发。所以对于新手而言，go-micro是个不错的微服务实践的开始。

## 二、go-micro 架构 {#item-2}

### 2.1 分层架构 {#item-2-1}

下图为[官方博客](https://micro.mu/docs/go-micro-architecture.html#)提供的go-micro架构图：

![](https://segmentfault.com/img/bVblToP?w=753&h=164 "clipboard.png")

go-micro是组件化的框架，每一个基础功能都是一个interface，方便扩展。同时，组件又是分层的，上层基于下层功能向上提供服务，整体构成go-micro框架。  
go-micro的组件包括：

* Registry组件：服务发现组件，提供服务发现机制：解析服务名字至服务地址。目前支持的注册中心有consul、etcd、 zookeeper、dns、gossip等
* Selector组件：构建在Registry之上的客户端智能负载均衡组件，用于Client组件对Registry返回的服务进行智能选择。
* Broker组件：发布/订阅组件，服务之间基于消息中间件的异步通信方式，默认使用http方式，线上通常使用消息中间件，如Kafka、RabbitMQ等。
* Transport组件：服务之间同步通信方式。
* Codec组件：服务之间消息的编码/解码。
* Server组件：服务主体，该组件基于上面的Registry/Selector/Transport/Broker组件，对外提供一个统一的服务请求入口。
* Client组件：提供访问微服务的客户端。类似Server组件，它也是通过Registry/Selector/Transport/Broker组件实现查找服务、负载均衡、同步通信、异步消息等功能。

所有以上组件功能共同构成一个go-micro微服务。

### 2.2 微服务之间通信 {#item-2-2}

两个微服务之间的通信是基于C/S模型，即服务发请求方充当Client，服务接收方充当Server。  
其通信过程大致如下图：  
![](https://segmentfault.com/img/bVblToQ?w=1017&h=438 "clipboard.png")

上图大致描绘了go-micro服务内各组件的职责与交互。

