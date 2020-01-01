# go-micro gateway实践

[https://github.com/golang-study-day/go-micro](https://github.com/golang-study-day/go-micro)



go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway



MyPATH='/Users/didi/goLang'

protoc -I/usr/local/include -I. \

  -I$MyPATH/src \

  -I$MyPATH/src/github.com/grpc-ecosystem/grpc-gateway/third\_party/googleapis \

  --go\_out=plugins=grpc:. \

  proto/hello.proto



  protoc -I/usr/local/include -I. \

    -I$MyPATH/src \

    -I$MyPATH/src/github.com/grpc-ecosystem/grpc-gateway/third\_party/googleapis \

    --grpc-gateway\_out=logtostderr=true:. \

    proto/gateway.proto





$vi ~/.bashrc

    alias protoc=/usr/local/bin/protoc



/Users/didi/goLang/src//src: warning: directory does not exist.

/Users/didi/goLang/src//src/github.com/grpc-ecosystem/grpc-gateway/third\_party/googleapis: warning: directory does not exist.



git clone https://github.com/grpc-ecosystem/grpc-gateway





$tree

.

\|\_\_\_\_gateway

\|  \|\_\_\_\_gateway.go

\|\_\_\_\_proto

\| \|\_\_\_\_hello.pb.go

\| \|\_\_\_\_hello.proto

\| \|\_\_\_\_gateway.pb.gw.go

\| \|\_\_\_\_gateway.proto

\|\_\_\_\_README

\|\_\_\_\_server

\| \|\_\_\_\_hello.go



\# github.com/micro/go-micro/registry/etcd

src/github.com/micro/go-micro/registry/etcd/etcd.go:81:21: cannot use cfg \(type \*"go.uber.org/zap".Config\) as type \*"go.etcd.io/etcd/vendor/go.uber.org/zap".Config in assignment



Process finished with exit code 2



$govendor init

$govendor add +e



\# github.com/golang-study-day/go-micro/exp3/vendor/google.golang.org/grpc/resolver/dns

src/github.com/golang-study-day/go-micro/exp3/vendor/google.golang.org/grpc/resolver/dns/dns\_resolver.go:131:45: unknown field 'MaxDelay' in struct literal of type "github.com/golang-study-day/go-micro/exp3/vendor/google.golang.org/grpc/internal/backoff".Exponential



\# command-line-arguments

src/github.com/golang-study-day/go-micro/exp3/server/hello.go:32:40: cannot use service.Server\(\) \(type server.Server\) as type \*grpc.Server in argument to greeter.RegisterSayServer



Process finished with exit code 2





  curl -d '{"name": "john"}' http://localhost:8080/greeter/hello

{"error":"connection error: desc = \"transport: Error while dialing dial tcp \[::1\]:9090: connect: connection refused\"","code":14,"message":"connection error: desc = \"transport: Error while dialing dial tcp \[::1\]:9090: connect: connection refused\""}







https://github.com/micro/examples



/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp3/server/hello.go

2020-01-01 22:40:37 Starting \[service\] go.micro.srv.greeter

2020-01-01 22:40:37 Server \[grpc\] Listening on \[::\]:58441

2020-01-01 22:40:37 Broker \[http\] Connected to \[::\]:58442

2020-01-01 22:40:37 Registry \[mdns\] Registering node: go.micro.srv.greeter-fa57b99e-9564-463e-95be-2318052e0aba





/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp3/server/hello.go

2020-01-01 22:47:48 Starting \[service\] go.micro.srv.greeter

2020-01-01 22:47:48 Server \[grpc\] Listening on \[::\]:58514

2020-01-01 22:47:48 Broker \[http\] Connected to \[::\]:58515

2020-01-01 22:47:48 Registry \[consul\] Registering node: go.micro.srv.greeter-d25ffa54-1823-4baa-a593-0f7d7b7f256d

2020-01-01 22:47:48 Server register error: Put http://127.0.0.1:9090/v1/agent/service/register: dial tcp 127.0.0.1:9090: connect: connection refused



$consul agent -dev



短裤换成 8500



/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp3/server/hello.go

listen :9090

2020-01-01 23:01:27.710104 I \| Received Say.Hello request

2020-01-01 23:01:30.766613 I \| Received Say.Hello request







$  curl -d '{"name": "john"}' http://localhost:8080/greeter/hello

{"msg":"Hello john"}





