# 编写Golang服务

编写服务

顶级的服务接口Service，是构建服务所需的主要组件。它把所有Go-Micror的基础包打包成单一组件接口。

type Service interface {

```
Init\(...Option\)

Options\(\) Options

Client\(\) client.Client

Server\(\) server.Server

Run\(\) error

String\(\) string
```

}

1. 初始化

可以使用micro.NewService创建服务

import "github.com/micro/go-micro"

service := micro.NewService\(\)

初始化时，也可以传入相关选项

service := micro.NewService\(

```
    micro.Name\("greeter"\),

    micro.Version\("latest"\),
```

\)

所有的可选参数参考：配置项

Go Micro也提供通过命令行参数micro.Flags传递配置参数：

import \(

```
    "github.com/micro/cli"

    "github.com/micro/go-micro"
```

\)

service := micro.NewService\(

```
    micro.Flags\(

            cli.StringFlag{

                    Name:  "environment",

                    Usage: "The environment",

            },

    \)
```

\)

解析命令行标识参数可以使用service.Init，增加标识参数可以使用micro.Action选项：

service.Init\(

```
    micro.Action\(func\(c \*cli.Context\) {

            env := c.StringFlag\("environment"\)

            if len\(env\) &gt; 0 {

                    fmt.Println\("Environment set to", env\)

            }

    }\),
```

\)

Go Micro提供预置的标识，service.Init执行时就会设置并解析这些参数。所有的标识参考.

1. 定义API

我们使用protobuf文件来定义服务的API接口。使用protobuf可以非常方便去严格定义API，提供服务端与客户端双边具体一致的类型。

下面是定义的示例

greeter.proto

syntax = "proto3";

service Greeter {

```
rpc Hello\(HelloRequest\) returns \(HelloResponse\) {}
```

}

message HelloRequest {

```
string name = 1;
```

}

message HelloResponse {

```
string greeting = 2;
```

}

我们定义了一个服务叫做Greeter的处理器，它有一个接收HelloRequest并返回HelloResponse的Hello方法。

1. 生成API接口

我们需要下面这个工具来生成protobuf代码文件，它们负责生成定义的go代码实现。

protoc

protoc-gen-go

protoc-gen-micro

go get github.com/golang/protobuf/{proto,protoc-gen-go}

go get github.com/micro/protoc-gen-micro

protoc --proto\_path=$GOPATH/src:. --micro\_out=. --go\_out=. greeter.proto

生成的类现在可以引入handler中，在服务或客户端来创建请求了。

下面是部分生成的代码

type HelloRequest struct {

    Name string \`protobuf:"bytes,1,opt,name=name" json:"name,omitempty"\`

}

type HelloResponse struct {

    Greeting string \`protobuf:"bytes,2,opt,name=greeting" json:"greeting,omitempty"\`

}

// Greeter service 客户端的API

type GreeterClient interface {

```
Hello\(ctx context.Context, in \*HelloRequest, opts ...client.CallOption\) \(\*HelloResponse, error\)
```

}

type greeterClient struct {

```
c           client.Client

serviceName string
```

}

func NewGreeterClient\(serviceName string, c client.Client\) GreeterClient {

```
if c == nil {

    c = client.NewClient\(\)

}

if len\(serviceName\) == 0 {

    serviceName = "greeter"

}

return &greeterClient{

    c:           c,

    serviceName: serviceName,

}
```

}

func \(c \*greeterClient\) Hello\(ctx context.Context, in \*HelloRequest, opts ...client.CallOption\) \(\*HelloResponse, error\) {

```
req := c.c.NewRequest\(c.serviceName, "Greeter.Hello", in\)

out := new\(HelloResponse\)

err := c.c.Call\(ctx, req, out, opts...\)

if err != nil {

    return nil, err

}

return out, nil
```

}

// Greeter service 服务端

type GreeterHandler interface {

```
Hello\(context.Context, \*HelloRequest, \*HelloResponse\) error
```

}

func RegisterGreeterHandler\(s server.Server, hdlr GreeterHandler\) {

```
s.Handle\(s.NewHandler\(&Greeter{hdlr}\)\)
```

}

1. 实现handler处理器

服务端需要注册handlers，这样才能提供服务并接收请求。处理器相当于是一个拥有公共方法的公共类，它需要符合签名func\(ctx context.Context, req interface{}, rsp interface{}\) error

通过上面的内容，我们看到，Greeter interface的签名的看上去就是这样：

type GreeterHandler interface {

```
    Hello\(context.Context, \*HelloRequest, \*HelloResponse\) error
```

}

Greeter处理器实现：

import proto "github.com/micro/examples/service/proto"

type Greeter struct{}

func \(g \*Greeter\) Hello\(ctx context.Context, req \*proto.HelloRequest, rsp \*proto.HelloResponse\) error {

```
rsp.Greeting = "Hello " + req.Name

return nil
```

}

处理器会与服务一起被注册，就像http处理器一样。

service := micro.NewService\(

```
micro.Name\("greeter"\),
```

\)

proto.RegisterGreeterHandler\(service.Server\(\), new\(Greeter\)\)

1. 运行服务

服务可以调用server.Run运行起来。这一步会让服务绑到配置中的地址（默认遵循RFC1918，分配随机的端口）接收请求。

另外，这一步会在服务启动时向注册中心注册，并在服务接收到关闭信号时卸载。

if err := service.Run\(\); err != nil {

```
log.Fatal\(err\)
```

}

1. 完成的服务

greeter.go

package main

import \(

```
    "log"



    "github.com/micro/go-micro"

    proto "github.com/micro/examples/service/proto"



    "golang.org/x/net/context"
```

\)

type Greeter struct{}

func \(g \*Greeter\) Hello\(ctx context.Context, req \*proto.HelloRequest, rsp \*proto.HelloResponse\) error {

```
    rsp.Greeting = "Hello " + req.Name

    return nil
```

}

func main\(\) {

```
    service := micro.NewService\(

            micro.Name\("greeter"\),

            micro.Version\("latest"\),

    \)



    service.Init\(\)



    proto.RegisterGreeterHandler\(service.Server\(\), new\(Greeter\)\)



    if err := service.Run\(\); err != nil {

            log.Fatal\(err\)

    }
```

}

需要注意的是，要保证服务发现机制运行起来，这样服务才能注册，其它服务或客户端才能发现它。快速启动可参考。

编写客户端

客户端包用于查询服务，当创建服务时，也包含了一个客户端，这个客户端匹配服务所使用的初始化包。

查询上面的服务很简单：

// 创建greate客户端，这需要传入服务名与服务的客户端方法构建的客户端对象

greeter := proto.NewGreeterClient\("greeter", service.Client\(\)\)

// 在Greeter handler上请求调用Hello方法

rsp, err := greeter.Hello\(context.TODO\(\), &proto.HelloRequest{

```
Name: "John",
```

}\)

if err != nil {

```
fmt.Println\(err\)

return
```

}

fmt.Println\(rsp.Greeter\)

proto.NewGreeterClient 需要服务名与客户端来请求服务。

完整示例参考：go-micro/examples/service.

