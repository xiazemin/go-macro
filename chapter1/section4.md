## 创建微服务

创建微服务

使用如下指令创建微服务



&gt;micro new techidea8.com/microapp/hello

Creating service go.micro.srv.hello in E:\winlion\gopath\src\techidea8.com\microapp\hello

 

.

├── main.go

├── plugin.go

├── handler

│   └── hello.go

├── subscriber

│   └── hello.go

├── proto\hello

│   └── hello.proto

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



