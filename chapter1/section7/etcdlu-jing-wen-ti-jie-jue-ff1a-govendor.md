1,

fatal: 无法访问 ‘https://go.googlesource.com/tools/’：Failed connect to go.googlesource.com:443; 拒绝连接

fatal: 无法克隆 ‘https://go.googlesource.com/tools’ 到子模组路径 ‘~/.vim/plugged/YouCompleteMe/third\_party/ycmd/third\_party/go/src/golang.org/x/tools’



进入~/.vim/plugged/YouCompleteMe/third\_party/ycmd/ 目录中：

git clone https://github.com/golang/tools



2,cannot find package "github.com/oxtoacart/bpool" in any of:

  	/usr/local/go/src/github.com/oxtoacart/bpool \(from $GOROOT\)

 go get -d -v ./...





 3,cannot find package "crypto/ed25519" in any of:



 ~/goLang/src/golang.org/x/crypto$git pull

 fatal: unable to access 'https://go.googlesource.com/crypto/': Failed to connect to go.googlesource.com port 443: Operation timed out



 $mv crypto/ crypto.bak

 $git clone https://github.com/golang/crypto.git





 依然不行,升级 1.13

 $export GOPROXY=https://goproxy.cn

 $export GO111MODULE=on

 go get -u -v github.com/micro/go-micro



 4,go:cannot find main module;see 'go help modules'报错无法运行



   $go get -d -v ./...

   go: cannot find main module, but found .git/config in /Users/didi/goLang/src/github.com/golang-study-day/go-micro

   	to create a module there, run:

   	cd ../.. && go mod init



   $go mod init

   go: creating new go.mod: module github.com/golang-study-day/go-micro



   $go get -d -v ./...

   go: finding golang.org/x/net latest

   go: downloading golang.org/x/net v0.0.0-20191209160850-c0dbc17a3553

   go: extracting golang.org/x/net v0.0.0-20191209160850-c0dbc17a3553



 5,$git clone github.com/oxtoacart/bpool



 6, cannot find package "google.golang.org/genproto/googleapis/rpc/status" in any of:



 https://github.com/googleapis/go-genproto



 7,cannot use auth.callOpts \(type \[\]"github.com/coreos/etcd/vendor/google.golang.org。



 go get github.com/coreos/etcd/clientv3



https://github.com/etcd-io/etcd/pull/10044\#issuecomment-417125341

 github自动为项目移动创建重定向，所以github.com/coreos/etcd现在重定向到github.com/etcd-io/etcd，这意味着如果你依赖于github.com/coreos/etcdmaster，或者这个PR或更新的git哈希，你是依赖管理器（或go get）将从中提取代码github.com/coreos/etcd（在重定向之后）github.com/etcd-io/etcd并下载包含导入到go.etcd.io/etcd包的etcd源，但它会认为代码是针对的github.com/coreos/etcd，因此它会认为github.com/etcd-io/etcd导入是针对不同的项目。我相信依赖管理器（或go get）也将go.etcd.io/etcd引入该项目，因为它看到了它的import语句，这意味着你的依赖项中有两个etcd的副本，它们之间有import语句，导致像你这样的错误消息所示。



 可能的修复：



 取决于发布的etcd版本（git标签，如v3.3.9）而不是master，因为所有发布的版本都coreos/etcd在引用中引用，并且可以正常工作。

 如果需要依赖master，则显式更新所有import语句go.etcd.io/etcd并验证没有直接或传递依赖性github.com/coreos/etcd。

 传递依赖案例：

 如果某些golang项目对etcd既有直接依赖也有间接依赖，那么它们可能具有以下内容：



 &lt;project1&gt; dependsOn &lt;github.com/coreos/etcd @ v3.2.1&gt;

 &lt;project1&gt; dependsOn &lt;project2&gt;

 &lt;project2&gt; dependsOn &lt;github.com/coreos/etcd @ v3.2.0&gt;



 这通常很好。依赖管理器（glide，godep，…）将执行版本冲突解决并决定使用“github.com/coreos/etcd @ v3.2.1”作为etcd版本，因为根据semver，它应该与v3兼容。 2.0从传递依赖到project2。最后你得到一个像这样的项目列表传递给go编译器：



 &lt;project1&gt;

 &lt;project2&gt;

 &lt;github.com/coreos/etcd @ v3.2.1&gt;



 但是如果将project1更新为使用go.etcd.io/etcd而不是coreos/etcd？然后你可以像一个依赖树：



 &lt;project1&gt; dependsOn &lt;go.etcd.io/etcd @ master&gt;

 &lt;project1&gt; dependsOn &lt;project2&gt;

 &lt;project2&gt; dependsOn &lt;github.com/coreos/etcd @ master&gt;



 对于这样的情况下，依赖关系管理已没有意识到，go.etcd.io/etcd并coreos/etcd在概念上是一样的东西，它甚至不不管他们是哪个版本。所以它导入了两个依赖项。并且go编译器会认为定义的所有类型都与类型go.etcd.io/etcd不同coreos/etcd，这会导致您显示的错误类型。传递给go编译器的项目列表将是：



 &lt;project1&gt;

 &lt;project2&gt;

 &lt;go.etcd.io/etcd @ master&gt;

 &lt;github.com/coreos/etcd @ master&gt;



 对于master遇到此问题的etcd的项目，最明显的快速修复是依赖于已发布的etcd版本（git标签，如v3.3.9）而不是master，因为所有已发布的版本都coreos/etcd在引用中引用，并且可以正常工作。



 打算使用的项目go.etcd.io/etcd（因为它们依赖于master，来自此PR或更新的git哈希，或者etcd v3.4 +）：



 必须显式更新其import语句go.etcd.io/etcd。

 必须解决其传递依赖性的问题。如果任何依赖关系使用etcd 3.3-首选的解决方案是与项目所有者合作以引入依赖的新版本go.etcd.io/etcd。

 当etcd v3.4.0发布时，它与v3.3.0之间将存在不兼容性，遗憾的是，依赖管理器将无法检测到（因为3.4+将来自go.etcd.io/etcd和3.3将来自coreos/etcd）。我有点担心这将如何影响生态系统，所以你可以提供一个确切的项目和依赖性的任何细节可能会有所帮助。但从根本上说，这是etd社区必须要解决的问题。



$go get github.com/micro/go-micro/registry/consul

go get github.com/micro/go-micro/registry/consul: module github.com/micro/go-micro@upgrade found \(v1.18.0\), but does not contain package github.com/micro/go-micro/registry/consul



原因,没有安装

https://github.com/micro/go-plugins



$go get github.com/micro/go-plugins

cd ~/goLang/src/github.com/micro



$git clone https://github.com/micro/go-plugins



$mkdir -p go.etcd.io/etcd/v3



~/goLang/src$cp -r github.com/coreos/etcd/\* go.etcd.io/etcd/





/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp1/Clients/helloclient.go

src/github.com/micro/go-micro/registry/etcd/etcd.go:16:2: cannot find package "github.com/coreos/etcd/clientv3" in any of:

	/usr/local/go/src/github.com/coreos/etcd/clientv3 \(from $GOROOT\)

	/Users/didi/PhpstormProjects/go/src/github.com/coreos/etcd/clientv3 \(from $GOPATH\)

	/Users/didi/goLang/src/github.com/coreos/etcd/clientv3

	/Users/didi/PhpstormProjects/go/src/git.xiaojukeji.com/gulfstream/oneConf/src/github.com/coreos/etcd/clientv3

src/github.com/micro/go-micro/registry/etcd/etcd.go:17:2: cannot find package "github.com/coreos/etcd/etcdserver/api/v3rpc/rpctypes" in any of:

	/usr/local/go/src/github.com/coreos/etcd/etcdserver/api/v3rpc/rpctypes \(from $GOROOT\)

	/Users/didi/PhpstormProjects/go/src/github.com/coreos/etcd/etcdserver/api/v3rpc/rpctypes \(from $GOPATH\)

	/Users/didi/goLang/src/github.com/coreos/etcd/etcdserver/api/v3rpc/rpctypes

	/Users/didi/PhpstormProjects/go/src/git.xiaojukeji.com/gulfstream/oneConf/src/github.com/coreos/etcd/etcdserver/api/v3rpc/rpctypes



vi go-micro/redistry/etcd/etcd.go



	//"github.com/coreos/etcd/clientv3"

	//"github.com/coreos/etcd/etcdserver/api/v3rpc/rpctypes"

	"go.etcd.io/etcd/clientv3"

	"go.etcd.io/etcd/etcdserver/api/v3rpc/rpctypes"



src/github.com/micro/go-micro/registry/etcd/watcher.go:8:2: cannot find package "github.com/coreos/etcd/clientv3" in any of:

	/usr/local/go/src/github.com/coreos/etcd/clientv3 \(from $GOROOT\)

	/Users/didi/PhpstormProjects/go/src/github.com/coreos/etcd/clientv3 \(from $GOPATH\)

	/Users/didi/goLang/src/github.com/coreos/etcd/clientv3

	/Users/didi/PhpstormProjects/go/src/git.xiaojukeji.com/gulfstream/oneConf/src/github.com/coreos/etcd/clientv3



vi src/github.com/micro/go-micro/registry/etcd/watcher.go:8:2:

	//"github.com/coreos/etcd/clientv3"

	"go.etcd.io/etcd/clientv3"



\# github.com/micro/go-micro/transport/quic

  src/github.com/micro/go-micro/transport/quic/quic.go:121:3: unknown field 'IdleTimeout' in struct literal of type quic.Config

  \# github.com/micro/go-micro/registry/etcd

  src/github.com/micro/go-micro/registry/etcd/etcd.go:81:21: cannot use cfg \(type \*"go.uber.org/zap".Config\) as type \*"go.etcd.io/etcd/vendor/go.uber.org/zap".Config in assignment





vi src/github.com/micro/go-micro/registry/etcd/etcd.go:81:21:

	//"go.uber.org/zap"

        "go.etcd.io/etcd/vendor/go.uber.org/zap"



~/goLang/src/github.com/golang-study-day/go-micro/exp1$cp -r ~/goLang/src/go.etcd.io/ vendor/go.etcd.io/

~/goLang/src/github.com/golang-study-day/go-micro/exp1$rm -rf vendor/go.etcd.io/etcd/vendor/

$cp -r ~/goLang/src/go.etcd.io/etcd/vendor/go.uber.org/ vendor/



~/goLang/src/github.com/golang-study-day/go-micro/exp1$mv ~/goLang/src/go.etcd.io/ ~/goLang/src/go.etcd.io.bak





还是解决不了,直接注释掉



govendor init

govendor add +e

问题解决



/usr/local/go/bin/go run /Users/didi/goLang/src/github.com/golang-study-day/go-micro/exp1/services/hello.go

2020-01-01 16:56:15 Starting \[service\] hellooo

2020-01-01 16:56:15 Server \[grpc\] Listening on \[::\]:55456

2020-01-01 16:56:15 Broker \[http\] Connected to \[::\]:55457

2020-01-01 16:56:15 Registry \[consul\] Registering node: hellooo-8404e149-cc99-4a09-9980-d9828f8c36d4





