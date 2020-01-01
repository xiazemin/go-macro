# 自动生成项目

一、下载安装

官方给的步骤：



go get -u github.com/micro/micro



//Or via Docker

//docker pull microhq/micro

我呢是在Windows上面学习，没有使用docker，因此使用第一种方法。不过使用中遇到了一点问题：



package golang.org/x/net/ipv4: directory "E:\\go\\Projects\\src\\golang.org\\x\\net\\ipv4" is not using a known version control system

package golang.org/x/net/ipv6: directory "E:\\go\\Projects\\src\\golang.org\\x\\net\\ipv6" is not using a known version control system

package golang.org/x/crypto/acme/autocert: unrecognized import path "golang.org/x/crypto/acme/autocert" \(https fetch: Get https://golang.org/x/crypto/acme/autocert?go-get=1: dial tcp 216.239.37.1:443: connectex: A

 connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.\)

前三个呢暂时不管，最后一个是golang.org包里面没有autocert这个包，被墙了下载不了，自己到网上下载，比如下载整个crypto包然后手动移动，链接https://github.com/golang/crypto在这里。

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



