# 编写Golang Function



本篇是指导大家使用go-micro的Function功能，Function是执行一次的服务。（译者按：这里Function并不等同与平常我们编写的函数，而是只执行一次的服务所以我没有直接翻译，以免引起误解）



如果想先从更高的角度了解相关的工具集，可以查看博客https://micro.mu/blog/2016/03/20/micro.html。



先写一个Function

Function作为顶级的接口，它是go-micro中函数式编程模型主要组件。它封装服务接口，并提供执行一次函数的能力。



// Function 是只执行一次的函数

type Function interface {

    // Inherits Service interface

    Service

    // Done signals to complete execution

    Done\(\) error

    // Handle registers an RPC handler

    Handle\(v interface{}\) error

    // Subscribe registers a subscriber

    Subscribe\(topic string, v interface{}\) error

}



1. 初始化

Function使用micro.NewFunction构建。



import "github.com/micro/go-micro"



function := micro.NewFunction\(\) 



构建时也可以传入选项参数。



function := micro.NewFunction\(

        micro.Name\("greeter"\),

        micro.Version\("latest"\),

\)



可选参数参考。



Go micro也可以通过micro.Flags解析命令行的传参。



import \(

        "github.com/micro/cli"

        "github.com/micro/go-micro"

\)



function := micro.NewFunction\(

        micro.Flags\(

                cli.StringFlag{

                        Name:  "environment",

                        Usage: "The environment",

                },

        \)

\)



命令行标记参数可以使用function.Init解析。增加参数可以使用micro.Action。



function.Init\(

        micro.Action\(func\(c \*cli.Context\) {

                env := c.StringFlag\("environment"\)

                if len\(env\) &gt; 0 {

                        fmt.Println\("Environment set to", env\)

                }

        }\),

\)



Go Micro提供了一些预定义的参数标记，这些标记在执行function.Init时解析。所有预定义的标记参数可以参考。



2. 定义API

我们使用protobuf文件来定义服务的API接口。使用protobuf可以非常方便去严格定义API，提供服务端与客户端双边具体一致的类型。



greeter.proto



syntax = "proto3";



service Greeter {

    rpc Hello\(HelloRequest\) returns \(HelloResponse\) {}

}



message HelloRequest {

    string name = 1;

}



message HelloResponse {

    string greeting = 2;

}



我们定义了一个服务叫做Greeter的Function处理器，它有一个接收HelloRequest并返回HelloResponse的Hello方法。



3. 生成API接口

我们需要protoc和protoc-gen-go来生成protobuf代码文件，它们负责生成定义的go代码实现。



Go-micro使用代码生成器生成客户端存根方法，这样可以像gRPC减少模板方法。这一步需要golang/protobuffork出来的插件github.com/micro/protobuf.



go get github.com/micro/protobuf/{proto,protoc-gen-go}

protoc --go\_out=plugins=micro:. greeter.proto



生成的类现在可以引入handler中，在服务或客户端来创建请求了。



下面是代码生成器生成的一部分代码。



type HelloRequest struct {

    Name string \`protobuf:"bytes,1,opt,name=name" json:"name,omitempty"\`

}



type HelloResponse struct {

    Greeting string \`protobuf:"bytes,2,opt,name=greeting" json:"greeting,omitempty"\`

}



// 定义Greeter客户端的接口



type GreeterClient interface {

    Hello\(ctx context.Context, in \*HelloRequest, opts ...client.CallOption\) \(\*HelloResponse, error\)

}



type greeterClient struct {

    c           client.Client

    serviceName string

}



func NewGreeterClient\(serviceName string, c client.Client\) GreeterClient {

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

}



func \(c \*greeterClient\) Hello\(ctx context.Context, in \*HelloRequest, opts ...client.CallOption\) \(\*HelloResponse, error\) {

    req := c.c.NewRequest\(c.serviceName, "Greeter.Hello", in\)

    out := new\(HelloResponse\)

    err := c.c.Call\(ctx, req, out, opts...\)

    if err != nil {

        return nil, err

    }

    return out, nil

}



// Greeter的服务端API



type GreeterHandler interface {

    Hello\(context.Context, \*HelloRequest, \*HelloResponse\) error

}



func RegisterGreeterHandler\(s server.Server, hdlr GreeterHandler\) {

    s.Handle\(s.NewHandler\(&Greeter{hdlr}\)\)

}



4. 实现处理器

服务端需要注册handlers，这样才能提供服务并接收请求。处理器相当于是一个拥有公共方法的公共类，它需要符合签名func\(ctx context.Context, req interface{}, rsp interface{}\) error。



通过上面的内容，我们看到，Greeter interface的签名的看上去就是这样：



type GreeterHandler interface {

        Hello\(context.Context, \*HelloRequest, \*HelloResponse\) error

}



Greeter处理器实现。



import proto "github.com/micro/examples/service/proto"



type Greeter struct{}



func \(g \*Greeter\) Hello\(ctx context.Context, req \*proto.HelloRequest, rsp \*proto.HelloResponse\) error {

    rsp.Greeting = "Hello " + req.Name

    return nil

}



处理器注册过程和http.Handler很像。



function := micro.NewFunction\(

    micro.Name\("greeter"\),

\)



proto.RegisterGreeterHandler\(service.Server\(\), new\(Greeter\)\)



另外，Function接口也提供更简单的注册方式。



function := micro.NewFunction\(

        micro.Name\("greeter"\),

\)



function.Handle\(new\(Greeter\)\)



也可以使用Subscribe方法注册成异步的订阅者。



5. 运行Function

运行Function可以通过function.Run。这样它会绑定到配置中指定的地址（默认使用RFC1918规则来分配并生成随机端口），然后开始侦听端口。



另外，这一步会在服务启动时向注册中心注册，并在服务接收到关闭信号时卸载。



if err := function.Run\(\); err != nil {

    log.Fatal\(err\)

}



有接受服务请求后，这人Function就会退出。可以使用micro run 来管理Funtion的生命周期。完整的例子查看：examples/function.



6. 完整的函数

greeter.go



package main



import \(

        "log"



        "github.com/micro/go-micro"

        proto "github.com/micro/examples/function/proto"



        "golang.org/x/net/context"

\)



type Greeter struct{}



func \(g \*Greeter\) Hello\(ctx context.Context, req \*proto.HelloRequest, rsp \*proto.HelloResponse\) error {

        rsp.Greeting = "Hello " + req.Name

        return nil

}



func main\(\) {

        function := micro.NewFunction\(

                micro.Name\("greeter"\),

                micro.Version\("latest"\),

        \)



        function.Init\(\)



        function.Handle\(new\(Greeter\)\)



        if err := function.Run\(\); err != nil {

                log.Fatal\(err\)

        }

}



需要注意的是，要保证服务发现机制运行起来，这样服务才能注册，其它服务或客户端才能发现它。快速启动可参考。



编写客户端

客户端包用于查询服务，当创建服务时，也包含了一个客户端，这个客户端匹配服务所使用的初始化包。



查询上面的服务很简单：



// 创建greate客户端，这需要传入服务名与服务的客户端方法构建的客户端对象

greeter := proto.NewGreeterClient\("greeter", function.Client\(\)\)



// 在Greeter handler上请求调用Hello方法

rsp, err := greeter.Hello\(context.TODO\(\), &proto.HelloRequest{

    Name: "John",

}\)

if err != nil {

    fmt.Println\(err\)

    return

}



fmt.Println\(rsp.Greeter\)



proto.NewGreeterClient 需要Function名与客户端来请求服务。



完整例子可查看go-micro/examples/function.



