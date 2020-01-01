# Go 服务注册--consul

1 .安装consul - 注册中心

服务注册中心我们选择consul：



mac：brew install consul



windows：直接官网下载consul.exe可执行程序



 运行consul：启动Consul agent的开发模式：



   consul agent -dev



该命令快速启动一个单节点的consul，且为集群的领袖



查看Consul集群的成员：打开另一个终端执行：



consul members







也可以通过WebUI来查看各service状态：http://localhost:8500/



