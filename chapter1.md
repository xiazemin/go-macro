# 实现一个简单的基于go-micro的微服务

为简单起见，client与server之间我们使用点对点的同步方式（Transport），即无需消息中间件（Broker），注册中心采用consul系统。

### 3.1 安装[consul](https://book-consul-guide.vnzmi.com/01_what_is_consul.html)- 注册中心 {#item-3-3}

服务注册中心我们选择consul：

* mac：
  `brew install consul`
* windows：直接
  [官网下载consul.exe](https://www.consul.io/downloads.html)
  可执行程序

运行consul：启动Consul agent的开发模式：

`consul agent -dev`

该命令快速启动一个单节点的consul，且为集群的领袖

查看Consul集群的成员：打开另一个终端执行：

`consul members`

停止Agent：使用 Ctrl-C，优雅的关闭Agent

也可以通过WebUI来查看各service状态：[http://localhost](http://localhost/):8500/

### 3.2 安装[micro](https://github.com/micro/micro)：微服务管理工具 {#item-3-4}

micro是以go-micro框架为核心的微服务管理工具，通过它可以方便查看go-micro服务情况。

在$GOPATH目录下，执行`go get github.com/micro/micro`，该命令会在bin目录（$GOBIN）下生成micro\(.exe\)工具  
micro命令行工具可以提供诸如服务列表查看、服务详情查看、调用服务接口等功能。

### 3.3 安装goprotobuf相关工具：GRPC相关工具 {#item-3-5}

* protoc：Protobuf（Protocol Buffers - Google's data interchange format）编译器：

  * windows下直接
    [下载](https://github.com/protocolbuffers/protobuf/releases)
    相关win的zip压缩文件（内含protoc.exe）
  * mac：
    `brew install protobuf`

* protoc-gen-go：goprotobuf 提供的 Protobuf 插件：在$GOPATH目录下执行
  `go get github.com/micro/protobuf/{proto,protoc-gen-go}`
  ，该命令会在bin目录下生成protoc-gen-go\(.exe\)工具，protoc编译器利用protoc-gen-go插件将.proto文件转换为Golang源文件
* protoc-gen-micro（Protobuf code generation for micro）：在$GOPATH目录下执行
  `go get github.com/micro/protoc-gen-micro`
  ，该命令会在bin目录下生成protoc-gen-micro\(.exe\)，protoc编译器利用protoc-gen-micro插件将.proto文件转换为micro代码风格文件

goprotobuf编译参数：

* `-I`
  参数：指定import路径，可以指定多个
  `-I`
  参数，编译时按照顺序查找，不指定时默认查找当前目录
* `--go_out`：Golang编译支持，支持以下参数

      -
      `plugins=plugin1+plugin2`
      ：指定插件，支持grpc/micro，即：plugins=grpc+micro

      -
      `M`
      参数：指定导入的.proto文件路径编译后对应的goalng包名（不指定默认.proto文件中import语句路径）

      -
      `import_prefix=xxx`
      ：为所有import路径添加前缀，主要用于编译子目录内的多个proto文件

      -
      `import_path=foo/bar`
      ：指定未声明package或go_package的文件的包名，最右边的斜线前的字符会被忽略





