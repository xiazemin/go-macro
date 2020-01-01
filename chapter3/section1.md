# micro的插件plugin

例子的github地址： gomicrorpc   跑一遍例子，也就会明白个大概。



安装所需要的环境

    go-micro服务发现默认使用的是consul，\(2019年源码修改了默认使用mdns\)



brew install consul

consul agent -dev

   或者直接使用使用docker跑



docker run -p 8300:8300 -p 8301:8301 -p 8301:8301/udp -p 8302:8302/udp -p 8302:8302 -p 8400:8400 -p 8500:8500 -p 53:53/udp consul

    我个人更喜欢etcdv3原因我上一篇也有提到过，gomicro服务发现不支持consul集群，我之前也写过etcdv3 集群的搭建和使用帖子，有时间大家可以看一下



    安装go-micro框架



go get github.com/micro/go-micro

    安装protobuf和依赖 prtobuf的基础知识我这里就不讲了，如果不了解的可以看一下官方文档，就是一个跨平台，跨语言的数据序列化库，简单易学。



    是go-micro用于帮助我们生成服务接口和一系列的调用代码



brew install protobuf

go get -u -v github.com/golang/protobuf/{proto,protoc-gen-go}

go get -u -v github.com/micro/protoc-gen-micro

    protobuf也可以直接从源码安装



复制代码

wget https://github.com/protocolbuffers/protobuf/releases/download/v3.6.1/protobuf-all-3.6.1.tar.gz

tar zxvf protobuf-all-3.6.1.tar.gz

cd protobuf-3.6.1/

./autogen.sh

./configure

 make

make install

protoc -h

复制代码

　



    安装micro工具包，这个安装是可选项，micro提供了一系列的工具来帮助我们更好的使用go-micro。



go get github.com/micro/micro

 



例子1

创建proto文件common.proto，这个文件包含了传入和返回的参数，参数包含了常用的基础类型、数组、map等。还有一个Say 服务，这个服务里有一个rpc方法。



复制代码

syntax = "proto3";



package model;



message SayParam {

    string msg = 1;

}



message Pair {

    int32 key = 1;

    string values = 2;

}



message SayResponse {

    string msg = 1;

    // 数组

    repeated string values = 2;

    // map

    map&lt;string, Pair&gt; header = 3;

    RespType type = 4;

}



enum RespType {

    NONE = 0;

    ASCEND = 1;

    DESCEND = 2;

}



// 服务接口

service Say {

    rpc Hello\(SayParam\) returns \(SayResponse\) {}

}

复制代码

 



在根目录下运行，生成两个模板文件



  protoc --proto\_path=$GOPATH/src:. --micro\_out=. --go\_out=. example1/proto/\*.proto 

一个文件是proto的go 结构文件，还有一个go-micro rpc的接口文件。







server 端：



复制代码

type Say struct {}



func \(s \*Say\) Hello\(ctx context.Context, req \*model.SayParam, rsp \*model.SayResponse\) error {

    fmt.Println\("received", req.Msg\)

    rsp.Header = make\(map\[string\]\*model.Pair\)

    rsp.Header\["name"\] = &model.Pair{Key: 1, Values: "abc"}



    rsp.Msg = "hello world"

    rsp.Values = append\(rsp.Values, "a", "b"\)

    rsp.Type = model.RespType\_DESCEND



    return nil

}





func main\(\) {

    // 我这里用的etcd 做为服务发现，如果使用consul可以去掉

    reg := etcdv3.NewRegistry\(func\(op \*registry.Options\){

        op.Addrs = \[\]string{

            "http://192.168.3.34:2379", "http://192.168.3.18:2379", "http://192.168.3.110:2379",

        }

    }\)



    // 初始化服务

    service := micro.NewService\(

        micro.Name\("lp.srv.eg1"\),

        micro.Registry\(reg\),

    \)

    service.Init\(\)

    // 注册 Handler

    model.RegisterSayHandler\(service.Server\(\), new\(Say\)\)



    // run server

    if err := service.Run\(\); err != nil {

        panic\(err\)

    }

}

复制代码

服务发现我使用的是etcdv3  替换了默认的consul



micro.NewService 初始化服务，然后返回一个Service接口的实例，newService\(\)方法的大概流程如下，







 



先是给各个接口初始化默认值，再使用传入的值替换默认值，这也是go-micro可替换插件的地方。



service有一个Init\(\)可选方法，这是一个单例方法，



复制代码

func \(s \*service\) Init\(opts ...Option\) {

    // process options

    for \_, o := range opts {

        o\(&s.opts\)

    }



    s.once.Do\(func\(\) {

        // save user action

        action := s.opts.Cmd.App\(\).Action

        // set service action

        s.opts.Cmd.App\(\).Action = func\(c \*cli.Context\) {

                .........//这里就不把代码全显示出来了

                .........

        }

}

复制代码

用于始化cmd的一些信息



 service.Run\(\)方法 调用流程







   因为在初始化的时候没有指定端口，系统会自动分配一个端口号分给Server，并把这个server的信息注册到Register。



   BeferStart和AfterStart也都是可以自定义的



client 端：



复制代码

func main\(\) {

    // 我这里用的etcd 做为服务发现，如果使用consul可以去掉

    reg := etcdv3.NewRegistry\(func\(op \*registry.Options\){

        op.Addrs = \[\]string{

            "http://192.168.3.34:2379", "http://192.168.3.18:2379", "http://192.168.3.110:2379",

        }

    }\)



    // 初始化服务

    service := micro.NewService\(

        micro.Registry\(reg\),

    \)

    service.Init\(\)

    sayClent := model.NewSayService\("lp.srv.eg1", service.Client\(\)\)





    rsp, err := sayClent.Hello\(context.Background\(\), &model.SayParam{Msg: "hello server"}\)

    if err != nil {

        panic\(err\)

    }



    fmt.Println\(rsp\)



}

复制代码

 上面根据proto文件的生成的两个文件中有一个是rpc的接口文件，接口文件已经帮我们把调用方法的整个流程封装好了。



  只需要给出服务名称和licent就可以。然后调用Hello方法



  源码：



复制代码

func \(c \*sayService\) Hello\(ctx context.Context, in \*SayParam, opts ...client.CallOption\) \(\*SayResponse, error\) {

    req := c.c.NewRequest\(c.name, "Say.Hello", in\)

    out := new\(SayResponse\)

    err := c.c.Call\(ctx, req, out, opts...\)

    if err != nil {

        return nil, err

    }

    return out, nil

}

复制代码

 主要的流程里都在c.c.Call方法里。简单来说流程如下







 



就是得到节点信息address，根据address去查询 pool里是否有连接，如果有则取出来，如果没有则创建，然后进行数据传输，传输完成后把client放回到pool内。pool的大小也是可以控制的，这部分的代码读起来特别爽，具体的细节和处理流程会在以后的帖子里详细讲解



 例子2

    例子1，做了一个简单的服务，已经不能再简单了，只是为了能让大家熟悉一下go-micro。看完例子1后应该会有更多的想法，想使用更多的go-micro的功能，比如protobuf生成的类都在一起，如果想model和api分开怎么处理，怎么使用go-micro的双向流，怎么使用消息推送，等等。所以我就双做了一个小例子，这个例子里包含了一些东西。



    



    这个例子我就只说一下组织结构，也没有多少代码，大家有时间看一下就ok了。



    proto下的两个文件夹，一个model一个rpcapi，是把数据和api分开，api引用了model



看一下rpcapi



复制代码

syntax = "proto3";



package rpcapi;

import "github.com/lpxxn/gomicrorpc/example2/proto/model/common.proto";



// 服务接口

service Say {

    rpc Hello\(model.SayParam\) returns \(model.SayResponse\) {}

    rpc Stream\(model.SRequest\) returns \(stream model.SResponse\) {}



}

复制代码

    import了model里的common.proto



在生成的时候一个只要go\_out另一个只要micro\_out就好了



  protoc --proto\_path=$GOPATH/src:. --go\_out=. example2/proto/model/\*.proto 

  

  protoc --proto\_path=$GOPATH/src:. --micro\_out=. example2/proto/rpcapi/\*.proto 

    订阅一个信息



    // Register Subscribers

    if err := server.Subscribe\(server.NewSubscriber\(common.Topic1, subscriber.Handler\)\); err != nil {

        panic\(err\)

    }

   当有信息发送时，所有订阅了lp.srv.eg2.topic1这个信息的服务都会收到信息



   客户端发送信息



    p := micro.NewPublisher\(common.Topic1, service.Client\(\)\)

    p.Publish\(context.TODO\(\), &model.SayParam{Msg: lib.RandomStr\(lib.Random\(3, 10\)\)}\)

   如果是生产环境一定不要用go-micro默认的信息发布和订阅处理方式，micro的插件plugin里是有很多成熟的插件。



   使用双向流的小功能



   这个方法只是每次向客户端发送一些数据，每次只发送一部分。比如我们给客户端推送的数据很大时，一次性全都推过去，是不太正确的做法，分批推送还是比较好的。



复制代码

func \(s \*Say\) Stream\(ctx context.Context, req \*model.SRequest, stream rpcapi.Say\_StreamStream\) error {



    for i := 0; i &lt; int\(req.Count\); i++ {

        rsp := &model.SResponse{}

        for j := lib.Random\(3, 5\); j &lt; 10; j++ {

            rsp.Value = append\(rsp.Value, lib.RandomStr\(lib.Random\(3, 10\)\)\)

        }

        if err := stream.Send\(rsp\); err != nil {

            return err

        }

        // 模拟处理过程

        time.Sleep\(time.Microsecond \* 50\)

    }

    return nil



    return nil

}

复制代码

