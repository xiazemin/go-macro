## 创建微服务

创建微服务

使用如下指令创建微服务

&gt;micro new techidea8.com/microapp/hello

Creating service go.micro.srv.hello in E:\winlion\gopath\src\techidea8.com\microapp\hello

.

├── main.go

├── plugin.go

├── handler

│   └── hello.go

├── subscriber

│   └── hello.go

├── proto\hello

│   └── hello.proto

├── Dockerfile

├── Makefile

├── README.md

└── go.mod

download protobuf for micro:

brew install protobuf

go get -u github.com/golang/protobuf/{proto,protoc-gen-go}

go get -u github.com/micro/protoc-gen-micro

compile the proto file hello.proto:

cd E:\winlion\gopath\src\techidea8.com\microapp\hello

protoc --proto\_path=.:$GOPATH/src --go\_out=. --micro\_out=. proto/hello/hello.proto

## 生成适配proto的golang代码

注意：在win系统下$GOPATH环境变量无效,因此如上脚本将创建微服务失败,因此我们需要对如上脚本进行处理

\#切换到项目目录下

&gt;cd /d E:\winlion\gopath\src\techidea8.com\microapp\hello

\# 根据proto生成文件

&gt;protoc --proto\_path=. --go\_out=. --micro\_out=. proto/hello/hello.proto

启动应用

&gt;go run main.go

2019/08/19 13:00:46 Transport \[http\] Listening on \[::\]:54689

2019/08/19 13:00:46 Broker \[http\] Connected to \[::\]:54690

2019/08/19 13:00:46 Registry \[mdns\] Registering node: go.micro.srv.hello-4851dce2-ab5d-4e4c-801e-44dae5d93f26

2019/08/19 13:00:46 Subscribing go.micro.srv.hello-4851dce2-ab5d-4e4c-801e-44dae5d93f26 to topic: go.micro.srv.hello

2019/08/19 13:00:46 Subscribing go.micro.srv.hello-4851dce2-ab5d-4e4c-801e-44dae5d93f26 to topic: go.micro.srv.hello

查看是否启动

&gt;micro list services

go.micro.srv.hello

topic:go.micro.srv.hello

## 启动restful api接口支持支持

注意其中的--namespace参数,我们每一个微服务都属于一个命名空间,通过api暴露出来该命名空间后,满足go.micro.srv.\*格式的微服务都可以访问。如go.micro.srv.hello可以通过如下格式访问



http://127.0.0.1:8080/user/call

&gt;micro api --namespace=go.micro.srv

2019/08/19 13:07:11 Registering API Default Handler at /

2019/08/19 13:07:11 HTTP API Listening on \[::\]:8080

2019/08/19 13:07:11 Transport \[http\] Listening on \[::\]:54934

2019/08/19 13:07:11 Broker \[http\] Connected to \[::\]:54935

2019/08/19 13:07:11 Registry \[mdns\] Registering node: go.micro.api-1753185c-b8e1-49c4-aa0f-617f243a8e2a





