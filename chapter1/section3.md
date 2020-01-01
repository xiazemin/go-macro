# 搭建 go-micro环境



\#linux 下

export GO111MODULE=on

export GOPROXY=https://goproxy.io

\# windows下设置如下环境变量

setx GO111MODULE on

setx GOPROXY https://goproxy.io

\# 使用如下指令安装

go get -u -v github.com/micro/micro

go get -u -v github.com/micro/go-micro

如果没有git请自行安装git



\#下载地址

https://git-scm.com/downloads/

安装 protoc

访问如下网址



https://github.com/protocolbuffers/protobuf/releases

下载,不同的版本文件名称不一样,我们这里选择protoc-3.9.1-win64.zip



protoc-3.9.1-win64.zip

解压到目标文件架,我们以e:dev为例



e:\dev\protoc-3.9.1-win64

添加e:devprotoc-3.9.1-win64bin到环境变量path



安装protoc-gen-micro插件

这个插件主要作用是通过.proto文件生成适用于go-micro的代码



go get -u -v github.com/micro/protoc-gen-micro

安装 consul

下载windows版本



https://www.consul.io/downloads.html

解压到



e:\dev\consul

添加e:devconsul到环境变量path使用如下指查看是否安装成功,如下所示安装成功



&gt;consul

Usage: consul \[--version\] \[--help\] &lt;command&gt; \[&lt;args&gt;\]

Available commands are:

    acl            Interact with Consul's ACLs

    agent          Runs a Consul agent

    catalog        Interact with the catalog

    config         Interact with Consul's Centralized Configurations

    connect        Interact with Consul Connect

    debug          Records a debugging archive for operators

