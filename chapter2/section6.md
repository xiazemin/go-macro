# go-micro中使用consul



启动go-micro服务时，可以看到使用的服务注册是mdns。这节来了解下如何使用consul。默认的服务发现是在同一台机器上，在生产环境下，服务与服务发现部署到同一机器，这不是个很好的实践。所以把consul机器独立出来就很有必要。

下面以Go Micro框架文章中的服务为例，编写一个go-micro服务。服务端microserver\_consul.go文件



package main



import \(

	"context"

	"fmt"

	"github.com/micro/go-micro"

	"github.com/micro/go-micro/registry"

	"github.com/micro/go-micro/registry/consul"

\)



type Greeter struct {}



func \(g \*Greeter\) Hello\(ctx context.Context, req \*HelloRequest, rsp \*HelloResponse\) error {

	rsp.Greeting = "hello " + req.Name

	return nil

}



func main\(\) {

	reg := consul.NewRegistry\(func\(op \*registry.Options\) {

		op.Addrs = \[\]string{

			"127.0.0.1:8500",

		}

	}\)

	service := micro.NewService\(

		micro.Registry\(reg\),

		micro.Name\("helloworld"\), \)



	service.Init\(\)

	err := RegisterGreeterHandler\(service.Server\(\), new\(Greeter\)\)

	if err != nil {

		fmt.Println\("failed to register a handler: ", err\)

	}



	if err = service.Run\(\); err != nil {

		fmt.Println\("failed to run a service: ", err\)

	}

}



1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

Consul自带一个漂亮的、多种功能的Web用户界面。用户界面可以用来浏览所有的服务和节点，浏览所有的健康检查和他们的最新状态，以及读写键值数据，用户界面还自动支持多数据中心。在启动Consul Agent时使用-ui参数，可设置宿主用户界面。例如：



$ consul agent -dev -ui

1

用户界面使用与HTTP API相同的接口，在ui/路径下，默认地址是http://localhost:8500/ui。

consul默认的地址是127.0.0.1：8500，生产环境常常不是这个地址。在启动Consul代理的情况下，运行上面的文件。然后就可以在命令行看到：



2019/04/30 15:07:26 Transport \[http\] Listening on \[::\]:55576

2019/04/30 15:07:26 Broker \[http\] Connected to \[::\]:55577

2019/04/30 15:07:26 Registry \[consul\] Registering node: helloworld-6c8afa0e-edca-474d-a495-23f4c2ba20e2



1

2

3

4

可以看到mdns已经变成了consul，注册的节点id为：helloworld-6c8afa0e-edca-474d-a495-23f4c2ba20e2。你在用户界面也可以看到有关节点的信息，访问http://localhost:8500/ui。

接着编写go-micro客户端文件microclient\_consul.go。



package main



import \(

	"context"

	"fmt"

	"github.com/micro/go-micro"

	"github.com/micro/go-micro/registry"

	"github.com/micro/go-micro/registry/consul"

\)



func main\(\) {

	reg := consul.NewRegistry\(func\(op \*registry.Options\) {

		op.Addrs = \[\]string{

			"127.0.0.1:8500",

		}

	}\)



	service := micro.NewService\(micro.Registry\(reg\), micro.Name\("greeter.client"\)\)

	service.Init\(\)

	rsp, err := NewGreeterService\("helloworld", service.Client\(\)\).Hello\(context.TODO\(\), &HelloRequest{Name: "benben\_2015"}\)

	if err != nil {

		fmt.Println\("failed to new greeter service: ", err\)

	}



	fmt.Println\(rsp.Greeting\)

}

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

运行客户端文件microclient\_consul.go后，你将会在命令行看到hello benben\_2015

————————————————

版权声明：本文为CSDN博主「benben\_2015」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/benben\_2015/article/details/89710442

