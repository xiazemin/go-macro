### 微服务管理工具

Go语言微服务库 github.com/micro/go-micro

由于Grpc存在ip和具体服务绑定，一旦服务ip改变，客户端代码也要改变，管理麻烦。同时，存在服务发现和服务间调用问题。直接使用实现了服务注册的 go-micro 框架。Micro是一套微服务构建工具库。对于微服务架构的应用，Micro提供平台层面、高度弹性的工具组件，让服务开发者们可以把复杂的分布式系统以简单的方式构建起来，并且尽可能让开发者使用最少的时间完成基础架构的构建。



安装微服务：

安装Go Micro

是基于Go语言用于开发的微服务的RPC框架。go get github.com/micro/go-micro



安装micro：微服务管理工具

micro是以go-micro框架为核心的微服务管理工具，通过它可以方便查看go-micro服务情况。在$GOPATH目录下，执行go get github.com/micro/micro，该命令会在bin目录（$GOBIN）下生成micro\(.exe\)工具，micro命令行工具可以提供诸如服务列表查看、服务详情查看、调用服务接口等功能。



安装依赖

依赖服务发现能力，默认使用Consul，下载链接：https://link.jianshu.com/?t=https%3A%2F%2Fwww.consul.io%2Fdownloads.html，之后可以将路径添加到path中。运行consul：启动Consul agent的开发模式：consul agent -dev。该命令快速启动一个单节点的consul，且为集群的领袖



查看Consul集群的成员：打开另一个终端执行：consul members。



停止Agent：使用 Ctrl-C，优雅的关闭Agent。



也可以通过WebUI来查看各service状态：http://localhost:8500/。



基于组播DNS，我们可以实现零依赖的服务发现。当启动参数--registry=mdns加到启动命令中，比如：micro --registry=mdns list services，不推荐。



安装goprotobuf相关工具，GRPC相关工具，提供字节传输格式，安装包在这里https://github.com/google/protobuf/releases，下载Windows下的压缩包，解压加环境变量即可使用exe文件来使用Protobuf的生成工具了。



protoc-gen-go：goprotobuf 提供的 Protobuf 插件：在$GOPATH目录下执行go get github.com/micro/protobuf/{proto,protoc-gen-go}，该命令会在bin目录下生成protoc-gen-go\(.exe\)工具，protoc编译器利用protoc-gen-go插件将.proto文件转换为Golang源文件



protoc-gen-micro（Protobuf code generation for micro）：在$GOPATH目录下执行go get github.com/micro/protoc-gen-micro，该命令会在bin目录下生成protoc-gen-micro\(.exe\)，protoc编译器利用protoc-gen-micro插件将.proto文件转换为micro代码风格文件。



goprotobuf编译参数：



-I参数：指定import路径，可以指定多个-I参数，编译时按照顺序查找，不指定时默认查找当前目录



--go\_out：Golang编译支持，支持以下参数



- \`plugins=plugin1+plugin2\`：指定插件，支持grpc/micro，即：plugins=grpc+micro



- \`M\`参数：指定导入的.proto文件路径编译后对应的goalng包名（不指定默认.proto文件中import语句路径）



- \`import\_prefix=xxx\`：为所有import路径添加前缀，主要用于编译子目录内的多个proto文件



- \`import\_path=foo/bar\`：指定未声明package或go\_package的文件的包名，最右边的斜线前的字符会被忽略。



编写go微服务

导入微服务库

import "github.com/micro/go-micro"



创建微服务

service := micro.NewService\(\)



初始化微服务

service := micro.NewService\(



       micro.Name\("greeter"\), //服务名字



       micro.Version\("latest"\), //版本



\)



命令行参数micro.Flags传递配置参数：



service := micro.NewService\(



        micro.Flags\(



                cli.StringFlag{



                        Name:  "environment",



                        Usage: "The environment",



                },



        \)



\)



解析命令行标识参数可以使用service.Init，增加标识参数可以使用micro.Action选项：



service.Init\(



        micro.Action\(func\(c \*cli.Context\) {



                env := c.StringFlag\("environment"\)



                if len\(env\) &gt; 0 {



                        fmt.Println\("Environment set to", env\)



                }



        }\),



\)



定义API

我们使用protobuf文件来定义服务的API接口。使用protobuf可以非常方便去严格定义API，提供服务端与客户端双边具体一致的类型。



syntax = "proto3";



service Greeter { //微服务结构



rpc Hello\(HelloRequest\) returns \(HelloResponse\) {} //注册方法



}



message HelloRequest {//消息结构 字符串 变量 标识符



string name = 1;



}



message HelloResponse {



string greeting = 2;



}



生成API接口

使用工具来生成protobuf代码文件，它们负责生成定义的go代码实现。



protoc --proto\_path=$GOPATH/src:. --micro\_out=. --go\_out=. greeter.proto



例如：protoc --proto\_path=F:\gocode\src\bottos\Go\service\proto --micro\_out=F:\gocode\src\bottos\Go\service\proto --go\_out=F:\gocode\src\bottos\Go\service\proto F:\gocode\src\bottos\Go\service\proto\greeter.proto



Protoc 是编译器的名字、--proto\_path指定对导入文件的搜索路径，若不指定，则为当前路径；--micro\_out指定生成micro风go RPC文件的路径， --go\_out为生成go文件rpc路径，最后为API文件。生成的类现在可以引入handler中，在服务或客户端来创建请求了。



实现handler处理器

服务端需要注册handlers，这样才能提供服务并接收请求。处理器相当于是一个拥有公共方法的公共类，它需要符合签名func\(ctx context.Context, req interface{}, rsp interface{}\) error。通过上面生成文件的内容，我们看到，Greeter interface的签名的看上去就是这样：



type GreeterHandler interface {



        Hello\(context.Context, \*HelloRequest, \*HelloResponse\) error



}



Greeter处理器实现：



import proto "github.com/micro/examples/service/proto"



type Greeter struct{}



func \(g \*Greeter\) Hello\(ctx context.Context, req \*proto.HelloRequest, rsp \*proto.HelloResponse\) error {



rsp.Greeting = "Hello " + req.Name



return nil



}



注意;观察micro文件，所有的客户端与服务端都是通过micro接口调用。文件前面为客户端接口，后面为客户端方法，后边为服务端接口，服务端接口需要在服务端实现，客户端直接通过新建客户端然后调用即可，简单方便。



注册服务和对应处理器

处理器会与服务一起被注册，就像http处理器一样。



service := micro.NewService\(



micro.Name\("greeter"\),



\)



proto.RegisterGreeterHandler\(service.Server\(\), new\(Greeter\)\)



运行服务

服务可以调用server.Run运行起来。这一步会让服务绑到配置中的地址（默认遵循RFC1918，分配随机的端口）接收请求。另外，这一步会在服务启动时向注册中心注册，并在服务接收到关闭信号时卸载。



ierr := service.Run\(\)



编写客户端

客户端包用于查询服务，当创建服务时，也包含了一个客户端，这个客户端匹配服务所使用的初始化包。创建greate客户端，这需要传入服务名与服务的客户端方法构建的客户端对象。



greeter := proto.NewGreeterClient\("greeter", service.Client\(\)\)



Greeter handler上请求调用Hello方法



rsp, err := greeter.Hello\(context.TODO\(\), &proto.HelloRequest{



Name: "John",



}\)



运行

 go run .\main.go  --registry=consul，显示微服务micro --registry=consul list services，--是指使用consul进行服务发现和注册。



