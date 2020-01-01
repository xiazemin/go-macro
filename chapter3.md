# Go 服务注册--consul

1 .安装consul - 注册中心

服务注册中心我们选择consul：

mac：brew install consul

windows：直接官网下载consul.exe可执行程序

运行consul：启动Consul agent的开发模式：

consul agent -dev

该命令快速启动一个单节点的consul，且为集群的领袖

查看Consul集群的成员：打开另一个终端执行：

consul members

也可以通过WebUI来查看各service状态：[http://localhost:8500/](http://localhost:8500/)

2 安装micro：微服务管理工具

```
  micro是以go-micro框架为核心的微服务管理工具，通过它可以方便查看go-micro服务情况。



 在$GOPATH目录下，执行
```

go get github.com/micro/micro

该命令会在bin目录（$GOBIN）下生成micro\(.exe\)工具

micro命令行工具可以提供诸如服务列表查看、服务详情查看、调用服务接口等功能。

3 安装goprotobuf相关工具：GRPC相关工具

protoc：Protobuf（Protocol Buffers - Google's data interchange format）编译器：

windows下直接下载 相关win的zip压缩文件（内含protoc.exe）

mac： brew install protobuf

protoc-gen-go：goprotobuf 提供的 Protobuf 插件：在$GOPATH目录下执行go get github.com/micro/protobuf/{proto,protoc-gen-go}，该命令会在bin目录下生成protoc-gen-go\(.exe\)工具，protoc编译器利用protoc-gen-go插件将.proto文件转换为Golang源文件

protoc-gen-micro（Protobuf code generation for micro）：在$GOPATH目录下执行go get github.com/micro/protoc-gen-micro，该命令会在bin目录下生成protoc-gen-micro\(.exe\)，protoc编译器利用protoc-gen-micro插件将.proto文件转换为micro代码风格文件

goprotobuf编译参数：

-I参数：指定import路径，可以指定多个-I参数，编译时按照顺序查找，不指定时默认查找当前目录

--go\_out：Golang编译支持，支持以下参数

* \`plugins=plugin1+plugin2\`：指定插件，支持grpc/micro，即：plugins=grpc+micro

* \`M\`参数：指定导入的.proto文件路径编译后对应的goalng包名（不指定默认.proto文件中import语句路径）

* \`import\_prefix=xxx\`：为所有import路径添加前缀，主要用于编译子目录内的多个proto文件

* \`import\_path=foo/bar\`：指定未声明package或go\_package的文件的包名，最右边的斜线前的字符会被忽略

4 编写一个简单的Hello服务

至此，go-micro框架的编程环境已基本搭建好，接下来就是写代码了。

下面实现一个Hello服务：它接收一个字符串类型参数请求，返回一个字符串问候语：Hello 『参数值』。

1）定义API

创建helloworld/helloworld.proto文件：

使用protobuf文件来定义服务API接口

// The greeting service definition.

service Hello {

rpc SayHello \(HelloRequest\) returns \(HelloReply\) {}

}

// The request message containing the user's name.

message HelloRequest {

string name = 1;

}

// The response message containing the greetings

message HelloReply {

string message = 1;

}

执行protoc命令，生成当前pb文件的go实现：

protoc  --micro\_out=. --go\_out=. ./helloworld/helloworld.proto

生成文件如下：

2）创建server

创建server/main.go文件：

package main

import \(

```
proto "awesomeProject/helloworld"

"context"

"fmt"

"github.com/micro/go-micro"

"github.com/micro/go-micro/registry"

"github.com/micro/go-micro/registry/consul"
```

\)

type Server struct {

}

func \(s \*Server\) SayHello\(ctx context.Context, in \*proto.HelloRequest, res \*proto.HelloReply\) error {

```
fmt.Printf\("%s", in.Name\)

res.Message="I am a server"

return nil
```

}

func main\(\) {

```
reg:=consul.NewRegistry\(func\(options \*registry.Options\) {

    options.Addrs=\[\]string{"127.0.0.1:8500",}

}\)

service := micro.NewService\(

    micro.Registry\(reg\),

    micro.Name\("helloworldServer001"\), // 服务名称

\)

service.Init\(\)

proto.RegisterHelloHandler\(service.Server\(\), new\(Server\)\)

if err := service.Run\(\); err != nil {

    fmt.Println\(err\)

}
```

}

3\)模拟client

创建client/main.go文件：

package main

import \(

```
proto "awesomeProject/helloworld"

"context"

"fmt"

"github.com/micro/go-micro"

"github.com/micro/go-micro/registry"

"github.com/micro/go-micro/registry/consul"
```

\)

const ADDRESS = "localhost:50051"

func main\(\) {

```
reg:=consul.NewRegistry\(func\(options \*registry.Options\) {

    options.Addrs=\[\]string{

        "127.0.0.1:8500",

    }

}\)

service := micro.NewService\(micro.Registry\(reg\),micro.Name\("hello.client"\)\)

service.Init\(\)

helloService := proto.NewHelloService\("helloworldServer001", service.Client\(\)\)

res, err := helloService.SayHello\(context.TODO\(\), &proto.HelloRequest{Name: "hellow ，I am Client--001"}\)

if err != nil {

    fmt.Println\(err\)

}

fmt.Println\("-----&gt;"+res.GetMessage\(\)\)
```

}

5 运行Hello服务

启动consul

执行go run server/main.go命令，启动服务：

&gt;go run server/main.go

2019/07/04 16:06:09 Transport \[http\] Listening on \[::\]:62381

2019/07/04 16:06:09 Broker \[http\] Connected to \[::\]:62382

2019/07/04 16:06:09 Registry \[consul\] Registering node: helloworldServer001-4ae1c14d-598f-4542-9fb3-cb295d9f00e6

1. 通过浏览器输入再次输入：[http://localhost:8500](http://localhost:8500), 查看当前已有服务：

1. 请求服务  执行 go run client/main.go



