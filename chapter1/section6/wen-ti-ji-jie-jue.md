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

│   └── hello.go

├── subscriber

│   └── hello.go

├── proto/hello

│   └── hello.proto

├── Dockerfile

├── Makefile

└── README.md

download protobuf for micro:

brew install protobuf

go get -u github.com/golang/protobuf/{proto,protoc-gen-go}

go get -u github.com/micro/protoc-gen-micro

compile the proto file hello.proto:

cd /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello

protoc --proto\_path=.:$GOPATH/src --go\_out=. --micro\_out=. proto/hello/hello.proto

3,生成代码

cd /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello

protoc --proto\_path=.:$GOPATH/src --go\_out=. --micro\_out=. proto/hello/hello.proto

\|\_\_\_\_proto

\| \|\_\_\_\_hello

\| \| \|\_\_\_\_hello.pb.go

\| \| \|\_\_\_\_hello.pb.micro.go

\# github.com/micro/go-micro/registry/etcd

src/github.com/micro/go-micro/registry/etcd/etcd.go:81:21: cannot use cfg \(type \*"go.uber.org/zap".Config\) as type \*"go.etcd.io/etcd/vendor/go.uber.org/zap".Config in assignment

Process finished with exit code 2

govendor init

govendor add +e

\# github.com/golang-study-day/go-micro/exp2/vendor/google.golang.org/grpc/resolver/dns

src/github.com/golang-study-day/go-micro/exp2/vendor/google.golang.org/grpc/resolver/dns/dns\_resolver.go:131:45: unknown field 'MaxDelay' in struct literal of type "github.com/golang-study-day/go-micro/exp2/vendor/google.golang.org/grpc/internal/backoff".Exponential

Process finished with exit code 2

type Exponential struct {

```
 // Config contains all options to configure the backoff algorithm.

 Config grpcbackoff.Config

 MaxDelay time.Duration
```

}

/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello/main.go

2020-01-01 17:46:40 Starting \[service\] go.micro.srv.hello

2020-01-01 17:46:40 Server \[grpc\] Listening on \[::\]:56390

2020-01-01 17:46:40 Broker \[http\] Connected to \[::\]:56391

2020-01-01 17:46:40 Registry \[mdns\] Registering node: go.micro.srv.hello-7e0658ed-23f3-4a61-9a97-1af5c66048f3

$micro list services

go.micro.http.broker

go.micro.srv.hello

