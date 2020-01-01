# GRPC 网关



GRPC 网关

本篇主要帮助大家在go-micro服务中使用grpc网关。



GRPC网关grpc-gateway是protoc的一个插件。它遵循gRPC中的服务定义，生成反向代理服务，这个代理就会把RESTful风格的JSON API转成gRPC请求。



我们使用go-grpc写后台服务。Go-GRPC是客户端与服务端的go-micro、grpc插件包装器。当调用grpc.NewService时，它会返回micro.Service服务。



代码

可以在examples/grpc中查找相关代码。



预置条件

我们需要下面准备工作：



安装protobuf

mkdir tmp

cd tmp

git clone https://github.com/google/protobuf

cd protobuf

./autogen.sh

./configure

make

make check

sudo make install



安装插件

go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway

go get -u github.com/micro/protobuf/protoc-gen-go



Greeter Service

例子中我们使用go-grpc创建了简单的greeter微服务。



下面的proto文件的代码：



syntax = "proto3";



package go.micro.srv.greeter;



service Say {

    rpc Hello\(Request\) returns \(Response\) {}

}



message Request {

    string name = 1;

}



message Response {

    string msg = 1;

}



服务的Go代码：



package main



import \(

    "log"

    "time"



    hello "github.com/micro/examples/greeter/srv/proto/hello"

    "github.com/micro/go-grpc"

    "github.com/micro/go-micro"



    "golang.org/x/net/context"

\)



type Say struct{}



func \(s \*Say\) Hello\(ctx context.Context, req \*hello.Request, rsp \*hello.Response\) error {

    log.Print\("Received Say.Hello request"\)

    rsp.Msg = "Hello " + req.Name

    return nil

}



func main\(\) {

    service := grpc.NewService\(

        micro.Name\("go.micro.srv.greeter"\),

        micro.RegisterTTL\(time.Second\*30\),

        micro.RegisterInterval\(time.Second\*10\),

    \)



    // optionally setup command line usage

    service.Init\(\)



    // Register Handlers

    hello.RegisterSayHandler\(service.Server\(\), new\(Say\)\)



    // Run server

    if err := service.Run\(\); err != nil {

        log.Fatal\(err\)

    }

}



GRPC Gateway

GRPC网关使用相同的proto文件配上http选项提供服务



syntax = "proto3";



package greeter;



import "google/api/annotations.proto";



service Say {

    rpc Hello\(Request\) returns \(Response\) {

        option \(google.api.http\) = {

            post: "/greeter/hello"

            body: "\*"

        };

    }

}



message Request {

    string name = 1;

}



message Response {

    string msg = 1;

}



通过下面的proto命令生成grpc的存根和反向代理



protoc -I/usr/local/include -I. \

  -I$GOPATH/src \

  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third\_party/googleapis \

  --go\_out=plugins=grpc:. \

  path/to/your\_service.proto



protoc -I/usr/local/include -I. \

  -I$GOPATH/src \

  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third\_party/googleapis \

  --grpc-gateway\_out=logtostderr=true:. \

  path/to/your\_service.proto



在下面的代码中我们写了一个简单的问候（greeter）服务API。类似的代码也会用来注册其它的端点。切记，网关需要端点地址及服务。





package main



import \(

    "flag"

    "net/http"



    "github.com/golang/glog"

    "github.com/grpc-ecosystem/grpc-gateway/runtime"

    "golang.org/x/net/context"

    "google.golang.org/grpc"



    hello "github.com/micro/examples/grpc/gateway/proto/hello"

\)



var \(

    // the go.micro.srv.greeter address

    endpoint = flag.String\("endpoint", "localhost:9090", "go.micro.srv.greeter address"\)

\)



func run\(\) error {

    ctx := context.Background\(\)

    ctx, cancel := context.WithCancel\(ctx\)

    defer cancel\(\)



    mux := runtime.NewServeMux\(\)

    opts := \[\]grpc.DialOption{grpc.WithInsecure\(\)}



    err := hello.RegisterSayHandlerFromEndpoint\(ctx, mux, \*endpoint, opts\)

    if err != nil {

        return err

    }



    return http.ListenAndServe\(":8080", mux\)

}



func main\(\) {

    flag.Parse\(\)



    defer glog.Flush\(\)



    if err := run\(\); err != nil {

        glog.Fatal\(err\)

    }

}



运行示例

现在运行greeter服务，指定mdns参数，因为现在我们还没有注册中心。



go run examples/grpc/greeter/srv/main.go --registry=mdns --server\_address=localhost:9090



运行网关程序，把greeter服务所有端点都放在localhost:9090下



go run examples/grpc/gateway/main.go



用Curl命令请求网关



curl -d '{"name": "john"}' http://localhost:8080/greeter/hello



局限性

我们需要向gRPC网关提供使用了服务发现、动态路由、负载均衡的后台micro api服务所在的地址。这降低了集成网关的通用性。



作者：承诺一时的华丽

链接：https://www.jianshu.com/p/e17b37982279

来源：简书

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

