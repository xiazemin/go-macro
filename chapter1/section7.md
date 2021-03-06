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
protoc --proto_path=.:$GOPATH/src --go_out=. --micro_out=. proto/hello/hello.proto


3,生成代码
cd /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello
protoc --proto_path=.:$GOPATH/src --go_out=. --micro_out=. proto/hello/hello.proto

|____proto
| |____hello
| | |____hello.pb.go
| | |____hello.pb.micro.go
```

问题是新版默认用etcd服务发现不是consul，会报下面错误

```
/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp2/hello/main.go
# github.com/micro/go-micro/transport/quic
src/github.com/micro/go-micro/transport/quic/quic.go:121:3: unknown field 'IdleTimeout' in struct literal of type quic.Config
# github.com/coreos/etcd/clientv3
src/github.com/coreos/etcd/clientv3/auth.go:121:72: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.AuthEnable
src/github.com/coreos/etcd/clientv3/auth.go:126:74: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.AuthDisable
src/github.com/coreos/etcd/clientv3/auth.go:131:152: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserAdd
src/github.com/coreos/etcd/clientv3/auth.go:136:144: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserAdd
src/github.com/coreos/etcd/clientv3/auth.go:141:86: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserDelete
src/github.com/coreos/etcd/clientv3/auth.go:146:122: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserChangePassword
src/github.com/coreos/etcd/clientv3/auth.go:151:104: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserGrantRole
src/github.com/coreos/etcd/clientv3/auth.go:156:80: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserGet
src/github.com/coreos/etcd/clientv3/auth.go:161:72: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserList
src/github.com/coreos/etcd/clientv3/auth.go:166:106: cannot use auth.callOpts (type []"github.com/coreos/etcd/vendor/google.golang.org/grpc".CallOption) as type []"go.etcd.io/etcd/vendor/google.golang.org/grpc".CallOption in argument to auth.remote.UserRevokeRole
src/github.com/coreos/etcd/clientv3/auth.go:166:106: too many errors
```

```
$go get github.com/micro/go-micro/registry/consul
go get github.com/micro/go-micro/registry/consul: module github.com/micro/go-micro@upgrade found (v1.18.0), but does not contain package github.com/micro/go-micro/registry/consul

原因,没有安装
https://github.com/micro/go-plugins

$go get github.com/micro/go-plugins
cd ~/goLang/src/github.com/micro

$git clone https://github.com/micro/go-plugins
```



