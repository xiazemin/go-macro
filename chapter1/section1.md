### 编写一个简单的Hello服务 {#item-3-6}



go-micro框架的编程环境已基本搭建好，接下来就是写代码了。



下面实现一个Hello服务：它接收一个字符串类型参数请求，返回一个字符串问候语：Hello 『参数值』。

1）定义API



创建proto/hello.proto文件：

使用protobuf文件来定义服务API接口



syntax = "proto3";

service Hello {

    rpc Ping\(Request\) returns \(Response\) {}

}

message Request {

    string name = 1;

}

message Response {

    string msg = 1;

}

执行protoc命令，生成当前pb文件的go实现：



protoc --go\_out=plugins=micro:. ./proto/hello.proto

2）创建service



创建services/hello.go文件：



package main



import \(

    "context"

    "fmt"



    proto "winmicro/proto"



    micro "github.com/micro/go-micro"

\)



type Hello struct{}



func \(h \*Hello\) Ping\(ctx context.Context, req \*proto.Request, res \*proto.Response\) error {

    res.Msg = "Hello " + req.Name

    return nil

}

func main\(\) {

    service := micro.NewService\(

        micro.Name\("hellooo"\), // 服务名称

    \)

    service.Init\(\)

    proto.RegisterHelloHandler\(service.Server\(\), new\(Hello\)\)

    if err := service.Run\(\); err != nil {

        fmt.Println\(err\)

    }

}

3\)模拟client



创建Clients/helloclient.go文件：



package main



import \(

    "context"

    "fmt"



    proto "winmicro/proto"



    micro "github.com/micro/go-micro"

\)



func main\(\) {

    service := micro.NewService\(micro.Name\("hello.client"\)\) // 客户端服务名称

    service.Init\(\)

    helloservice := proto.NewHelloService\("hellooo", service.Client\(\)\)

    res, err := helloservice.Ping\(context.TODO\(\), &proto.Request{Name: "World ^\_^"}\)

    if err != nil {

        fmt.Println\(err\)

    }

    fmt.Println\(res.Msg\)

}

