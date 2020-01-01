# 1，手动创建

```
brew install consul
brew install protobuf

export GO111MODULE=on
export GOPROXY=https://goproxy.io

go get github.com/micro/micro
go get -u -v github.com/micro/go-micro
go get github.com/micro/protobuf/{proto,protoc-gen-go}

#~/goLang/src/github.com/micro$ls
#cli            mdns            protobuf
#go-micro        micro            protoc-gen-micro

consul agent -dev
#==> Starting Consul agent...
#==> Consul agent running!
#           Version: 'v0.8.1'
#           Node ID: '04145f71-d5c1-a469-c270-026f3911e0b1'

consul members
#Node       Address         Status  Type    Build  Protocol  DC
#localhost  127.0.0.1:8301  alive   server  0.8.1  2         dc1

#http://localhost:8500/ui/#/dc1/services/consul

protoc --go_out=plugins=micro:. ./proto/hello.proto

tree
#.
#|____proto
#| |____hello.pb.go
#| |____hello.proto
#|____setup.sh
```

```
|____Clients
| |____helloclient.go
|____proto
| |____hello.pb.go
| |____hello.proto
|____README
|____services
| |____hello.go
|____setup.sh
```

# 2，自动动创建

```
1,-bash: micro: command not found
原因,go get 后并没有编译,因此需要编译

cd /Users/didi/goLang/src/github.com/micro/micro
go build -o ~/goLang/bin/micro ./


2,生成项目
$micro new github.com/golang-study-day/go-micro/exp2/hello
Creating service go.micro.srv.hello in /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello

.
├── main.go
├── generate.go
├── plugin.go
├── handler
│   └── hello.go
├── subscriber
│   └── hello.go
├── proto/hello
│   └── hello.proto
├── Dockerfile
├── Makefile
└── README.md


download protobuf for micro:

brew install protobuf
go get -u github.com/golang/protobuf/{proto,protoc-gen-go}
go get -u github.com/micro/protoc-gen-micro

compile the proto file hello.proto:

cd /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello
protoc --proto_path=.:$GOPATH/src --go_out=. --micro_out=. proto/hello/hello.proto


3,生成代码
cd /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello
protoc --proto_path=.:$GOPATH/src --go_out=. --micro_out=. proto/hello/hello.proto

|____proto
| |____hello
| | |____hello.pb.go
| | |____hello.pb.micro.go

```



