# 自动生成项目

一、下载安装

官方给的步骤：

go get -u github.com/micro/micro

//Or via Docker

//docker pull microhq/micro

我呢是在Windows上面学习，没有使用docker，因此使用第一种方法。不过使用中遇到了一点问题：

package golang.org/x/net/ipv4: directory "E:\go\Projects\src\golang.org\x\net\ipv4" is not using a known version control system

package golang.org/x/net/ipv6: directory "E:\go\Projects\src\golang.org\x\net\ipv6" is not using a known version control system

package golang.org/x/crypto/acme/autocert: unrecognized import path "golang.org/x/crypto/acme/autocert" \(https fetch: Get [https://golang.org/x/crypto/acme/autocert?go-get=1](https://golang.org/x/crypto/acme/autocert?go-get=1): dial tcp 216.239.37.1:443: connectex: A

connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.\)

前三个呢暂时不管，最后一个是golang.org包里面没有autocert这个包，被墙了下载不了，自己到网上下载，比如下载整个crypto包然后手动移动，链接[https://github.com/golang/crypto在这里。](https://github.com/golang/crypto在这里。)

好，已安装完毕。

二、安装依赖

The micro toolkit has two dependencies:

\*   \[Service Discovery\] - used for name resolution

\*   \[Protobuf\] - used for code generation

按照官方的说法，micro要使用两个依赖，一个是Service Discovery，一个是Protobuf；第一个是服务发现，微服务呢使用了这个功能，也就是自动注册服务并且自动被发现，啥意思呢，就是你增加一个服务提供给用户，原来呢我们要手动调整原来的接口，调整负载等，但是有Service Discovery这个东西呢就自动化帮助我们做这些事，如下表（来自这里）：

注册发现模式

然后第二个依赖是Protobuf，就是类似json转换的序列化工具。

下面说说这两个依赖怎么安装使用。

1、Service Discovery

这里使用默认的consul，这里是下载地址,下载相应版本，解压后

然后将此文件夹加入环境变量，方便我们在任何地方都能直接调用consul命令启动consul，当然也可以不加，那么每次启动都要进入该文件夹下启动。

使用命令：

consul agent -dev

即可开启consul的监听，这里只在一台电脑上开启微服务，那么暂时不用使用consul的其它命令，如果要在多台电脑远程开启多个微服务，或者使用docker开启多个微服务，就需要了解consul的命令了，这个我们后面再详细说。



2、Protobuf

Protobuf是啥呢，就是类似json、xml一样的东西，是一种轻便高效的结构化数据存储格式，但是它需要专门的工具来生成，方便我们快速开发。安装包在这里

https://github.com/google/protobuf/releases

我们可以在这里下载Windows下的压缩包，解压加环境变量即可使用exe文件来使用Protobuf的生成工具了。



将bin文件夹加入环境变量，或者里面的protoc.exe路径加入环境变量，这个exe文件就是我们转换protobuf的工具。

然后我们使用一些golang对于protobuf的工具，go get相应的库



\# install protoc-gen-go

go get -u github.com/golang/protobuf/{proto,protoc-gen-go}



\# install protoc-gen-micro

go get -u github.com/micro/protoc-gen-micro

用于自动化生成go-protobuf和go-micro需要的代码文件，使用这些工具将会非常方便。



三、运行测试（一）

先进入这个文件夹github.com/micro/micro，



cd GOPATH/src/github.com/micro/micro

然后执行go build或者直接



go build GOPATH/src/github.com/micro/micro

将在 GOPATH/bin文件夹下生成micro.exe文件，将GOPATH/bin文件夹路径加入环境变量后我们就可以直接运行micro命令了，如果没有在bin文件夹生成那么手动移到该文件夹。

然后使用下面的命令来自动生成代码



micro new github.com/micro/example

也可以不这样生成，可以生成到自己目录下



micro new hdy/micro/example

那么生成的项目就在GOPATH/src/hdy/micro/example



example/

    Dockerfile  \# A template docker file

    README.md   \# A readme with command used

    handler/    \# Example rpc handler

    main.go     \# The main Go program

    proto/      \# Protobuf directory

    subscriber/ \# Example pubsub Subscriber

然后进入项目文件夹



cd GOPATH/src/hdy/micro/example

运行下面的代码生成protobuf和micro代码文件



protoc --proto\_path=. --micro\_out=. --go\_out=. proto/example/example.proto

然后就是一个完整的微服务项目了，请注意：



请确保已经新开窗口运行 consul agent -dev

//运行微服务时会向consul注册这个微服务，因此要先运行consul



最后：



go run main.go

OK，顺利运行第一个微服务。



四、运行测试（二）

运行下面的代码：



micro list services

将输出：



consul

go.micro.srv.example

topic:topic.go.micro.srv.example

更详细的测试，请查看这里https://github.com/micro/micro的测试方法，代码都一样，输出应该一样，具体就不详细展开了，下一节我们再详细分析微服务的编写和部署等方面。



