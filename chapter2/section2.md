# micro工具库

Micro重点工作服务方向是满足构建可伸缩的系统。Micro包含了微服务的架构模式，并把它把转换成一系列构建平台的工具。Micro深入复杂的分布式系统，且要把其简化抽象成众所周知的方式暴露给开发者使用。

技术本身是日新月异的。而那些基础的技术栈也总是会发生变动。Micro工具插件化就是要处理这个问题。

Micro的愿景是，在任何技术栈中都可插拔，使用Micro构建永不过时的系统。

特性

micro工具库由以下几个部分组成：

API Gateway: API网关作为单一的http入口，它使用服务发现中查询的服务地址，把请求动态路由到具体服务。网关允许我们建立可伸缩的后台微服务架构，并且让工作在前端的公共API更健壮。Micro API基于服务发现拥有强大的路由能力，它可以处理http、gRPC、websocket、消息推送事件等等。

Interactive CLI: 交互式的命令行接口。CLI通过终端可以描述、查询、直接与平台和服务进行交互。CLI提供所有的命令让开发者明白微服务正在处理的事情。CLI也包含了交互模式。

Service Proxy: 服务代理，这是一个在Go Micro和MUCP协议之上构建的透明的代理服务。它将服务发现、负载均衡、消息编码、中间件、传输及代理插件卸载到单一位置。

可以单独运行或与服务一起运行。

Service Templates: 服务生成模板，目的是快速生成服务代码，让编写代码飞起来。Micro预置了一些模板用来编写服务。 保持相同的方式编写服务，提高效率。

SlackOps Bot: Slack小机器人插件，当它运行中服务中时，这个插件允许开发者通过Slack消息来操作平台。MicroBot插件提供聊天配置选项，这样就可以让团队可以通过向小机器人发送聊天消息来做一些我们希望它做的事，这里面当然也包含像动态发现服务一样创建slack命令。

Web Dashboard: Web管理控制台允许直接在Web页面上查看服务的运行情况，展示端点信息，请求与响应状态，甚至直接向服务进行查询。管理控制台也有CLI交互页面提供给开发者在线上处理，就像直接操作终端一样。

开始使用

安装Micro

依赖

服务发现

编写服务

示例

相关插件

安装Micro

go get -u github.com/micro/micro

也可以通过Docker安装

docker pull microhq/micro

依赖

micro工具库有两个依赖：

Service Discovery - 用于服务名解析

Protobuf - 代码生成工具

服务发现

服务发现用于服务名解析、路由、集中元数据。

Micro使用go-micro来向服务发现中心发送注册请求。

Consul

安装并运行Consul

\# install

brew install consul

\# run

consul agent -dev

mDNS

Micro内置了mDNS组播系统，这是一种零依赖的服务注册发现机制，它是区别于有注册中心的替代方案。

通过在启动指令中传入--registry=mdns 或者在环境变量中设置MICRO\_REGISTRY=mdns。

\# 使用命令行参数

micro --registry=mdns list services

\# 使用环境变量

MICRO\_REGISTRY=mdns micro list services\`

查看更多关于插件的资料：go-plugins.

Protobuf

Protobuf功能是代码生成，可以免去手写一些模板化的代码。

\# 安装protobuf

brew install protobuf

\# 安装golang的protobuf代码生成器 protoc-gen-go

go get -u github.com/golang/protobuf/{proto,protoc-gen-go}

\# 安装micro的protobuf插件 protoc-gen-micro

go get -u github.com/micro/protoc-gen-micro

更多资料请查看protoc-gen-micro。

编写服务

上面说过Micro是有模板生成功能可以快速生成服务应用。

详情可以翻阅：go-micro.

使用模板生成

下面演示使用micro new命令来快速生成一个示例服务

生成的服务会被放到$GOPATH的相对目录下：

micro new github.com/micro/example

刚这个命令生成的目录如下所示：

example/

```
Dockerfile  \# A template docker file

README.md   \# A readme with command used

handler/    \# Example rpc handler

main.go     \# The main Go program

proto/      \# Protobuf directory

subscriber/ \# Example pubsub Subscriber
```

然后使用protoc把proto方便生成go源码

protoc --proto\_path=. --micro\_out=. --go\_out=. proto/example/example.proto

下面就可以像运行其它go语言程序一下执行下面的命令了：

go run main.go

示例

刚我们通过micro new生成了命令并跑了起来，下面我们测一下。

列出所有服务

获取服务信息

调用服务

执行API

调用API

列出所有服务

通过服务发现注册的服务都可以被列出来

micro list services

相关服务：

consul

go.micro.srv.example

topic:topic.go.micro.srv.example

示例中的注册了的应用使用的限定了的注册名go.micro.srv.example，go.micro.srv是后台服务名的默认前缀

获取指定名的服务

每一个服务注册都是通过唯一的id、地址及元数据。

micro get service go.micro.srv.example

输出的结果：

service  go.micro.srv.example

version latest

ID  Address Port    Metadata

go.micro.srv.example-437d1277-303b-11e8-9be9-f40f242f6897   192.168.1.65    53545   transport=http,broker=http,server=rpc,registry=consul

Endpoint: Example.Call

Metadata: stream=false

Request: {

```
name string
```

}

Response: {

```
msg string
```

}

Endpoint: Example.PingPong

Metadata: stream=true

Request: {}

Response: {}

Endpoint: Example.Stream

Metadata: stream=true

Request: {}

Response: {}

Endpoint: Func

Metadata: subscriber=true,topic=topic.go.micro.srv.example

Request: {

```
say string
```

}

Response: {}

Endpoint: Example.Handle

Metadata: subscriber=true,topic=topic.go.micro.srv.example

Request: {

```
say string
```

}

Response: {}

调用服务

通过CLI命令行接口来调用RPC服务，这次查询我们使用JSON来发送：

micro call go.micro.srv.example Example.Call '{"name": "John"}'

输出的结果：

{

```
"msg": "Hello John"
```

}

可以查看cli文档来查看更多信息。

下面，我们试一下通过HTTP来调用服务。

执行API

Micro API本质上是一个http协议的网关接口，它会把动态路由到转到后台服务中。

MICRO\_API\_HANDLER=rpc \

MICRO\_API\_NAMESPACE=go.micro.srv \

micro api

说明信息：

MICRO\_API\_HANDLER http API的触发方法

MICRO\_API\_NAMESPACE API所属服务的命名空间�

调用API

发送一个POST请求到API：

curl -XPOST -H 'Content-Type: application/json' -d '{"name": "John"}' [http://localhost:8080/example/call](http://localhost:8080/example/call)

结果：

{"msg":"Hello John"}

更多详情信息翻看api doc。

相关插件

Micro是基于go-micro来开发插件。

Go-micro向分布式系统架构可抽离的基础部分提供抽象。

可插拔特性

下面列表出的Micro特性都是可插拔的：

broker - 发布订阅消息的代理

registry - 服务发现与注册

selector - 客户端负载均衡

transport - 传输，请求-响应或者双向流

client - 管理以上特性的客户端

server - 管理以上特性的服务端

Find plugins at go-plugins

使用插件

插件的集成很简单，只需要创建个文件并引用这些插件即可。

import \(

```
// etcd v3 registry

\_ "github.com/micro/go-plugins/registry/etcdv3"

// nats transport

\_ "github.com/micro/go-plugins/transport/nats"

// kafka broker

\_ "github.com/micro/go-plugins/broker/kafka"
```

\)

构建二进制文件

直接使用Go命令行工具链即可。

\# 本地打包

go build -i -o micro ./main.go ./plugins.go

\# 打包成docker镜像

CGO\_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags '-w' -i -o micro ./main.go ./plugins.go

激活插件

引用插件后如果要激活，那么可以使用命令行或者设置环境变量即可。

\# 命令行参数

micro --registry=etcdv3 --transport=nats --broker=kafka \[其它命令\]

\# 环境变量

```
MICRO_REGISTRY=etcdv3 MICRO_TRANSPORT=nats MICRO_BROKER=kafka micro [其它命令]
```



