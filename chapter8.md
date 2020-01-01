# 新建模板



micro new命令是快速生成Micro服务代码模板。



使用

创建新的服务，目录会创建到$GOPATH的相对目录。



micro new github.com/micro/foo



下面是常规操作的输出



micro new github.com/micro/foo



creating service go.micro.srv.foo

creating /Users/asim/checkouts/src/github.com/micro/foo

creating /Users/asim/checkouts/src/github.com/micro/foo/main.go

creating /Users/asim/checkouts/src/github.com/micro/foo/handler

creating /Users/asim/checkouts/src/github.com/micro/foo/handler/example.go

creating /Users/asim/checkouts/src/github.com/micro/foo/subscriber

creating /Users/asim/checkouts/src/github.com/micro/foo/subscriber/example.go

creating /Users/asim/checkouts/src/github.com/micro/foo/proto/example

creating /Users/asim/checkouts/src/github.com/micro/foo/proto/example/example.proto

creating /Users/asim/checkouts/src/github.com/micro/foo/Dockerfile

creating /Users/asim/checkouts/src/github.com/micro/foo/README.md



download protobuf for micro:



go get github.com/micro/protobuf/{proto,protoc-gen-go}



compile the proto file example.proto:



protoc -I/Users/asim/checkouts/src \

    --go\_out=plugins=micro:/Users/asim/checkouts/src \

    /Users/asim/checkouts/src/github.com/micro/foo/proto/example/example.proto



选项

对于一些特殊操作比如namespace, type, fqdn和alias：



micro new --fqdn com.example.srv.foo github.com/micro/foo



帮助信息



NAME:

   micro new - Create a new micro service



USAGE:

   micro new \[command options\] \[arguments...\]



OPTIONS:

   --namespace "go.micro"   Namespace for the service e.g com.example

   --type "srv"         Type of service e.g api, srv, web

   --fqdn           FQDN of service e.g com.example.srv.service \(defaults to namespace.type.alias\)

   --alias          Alias is the short name used as part of combined name if specified





