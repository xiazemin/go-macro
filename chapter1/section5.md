# go-micro依赖

go-micro是基于 Go语言实现的插件化 RPC 微服务框架，与go-kit，kite等微服务框架相比，它具有易上手、部署简单、工具插件化等优点。

名称    说明

OS    macOS Mojave 10.14.6

Golang    go1.13 darwin/amd64

Micro    micro version 1.11.3

G111MODULE    on

安装 micro 工具包

micro提供了一系列的工具来帮助我们更好的使用 go-micro。，如果没有安装 golang 请先安装 golang

如果安装方式一出错，提示包找不到可以用安装方式二

\# 安装方式一

go get -u -v github.com/micro/micro

cd $GOPATH/src/github.com/micro/micro

go build -o micro main.go

sudo cp micro /usr/local/bin/

\# 安装方式二

cd $GOPATH/src/github.com/micro/

git clone [https://github.com/micro/micro.git](https://github.com/micro/micro.git)

cd $GOPATH/src/github.com/micro/micro

go build -o micro main.go

sudo cp micro /usr/local/bin/

安装框架go-micro

script

1

go get -u -v github.com/micro/go-micro

安装 protoc

官方地址

script

\# 源码安装，下载最新版，目前是 v3.10.1

wget [https://github.com/protocolbuffers/protobuf/releases/download/v3.10.1/protobuf-all-3.10.1.tar.gz](https://github.com/protocolbuffers/protobuf/releases/download/v3.10.1/protobuf-all-3.10.1.tar.gz)

tar zxvf protobuf-all-3.10.1.tar.gz

cd protobuf-3.10.1/

./autogen.sh

./configure

make

make install

protoc -h

\# 或者

git clone [https://github.com/google/protobuf](https://github.com/google/protobuf)

cd protobuf

./autogen.sh

./configure

make

make check

sudo make install

安装 Go 版本的 protobuf 编译器 protoc-gen-go

script

1

go get -u -v github.com/golang/protobuf/protoc-gen-go

