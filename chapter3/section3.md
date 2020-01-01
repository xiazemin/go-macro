## 其它的注册中心

## 除了Consul，可以使用其它的注册中心吗

当然是可以的，服务的注册发现的实现机制是可插拔的，之所以使用Consul是因为它拥有的特性以及它足够简单。 比如：

### Etcd

如果你想使用etcd那你只需要引用etcd包，然后在启动的注册方式上标明使用的是etcd就行了。

```
import
(
_
"github.com/micro/go-plugins/registry/etcd"
)
```

```
service --registry=etcd --registry_address=127.0.0.1:2379


```

### 零依赖

micro专门为零依赖配置内置有一个多路广播DNS服务注册中心。

如果要使用，只需要在程序启动指令上传上`--registry=mdns`或者`MICRO_REGISTRY=mdns`。

## Micro可以在哪些环境运行

micro对运行环境不挑食。只要你喜欢，在哪都行，裸机, 亚马逊AWS或者Google Cloud，也可以运行在你喜欢的容器编排系统中比如：Mesos、Kubernetes。

右边的这个链接中有关于如何使用K8s来开发micro服务的[micro kubernetes demo](https://github.com/micro/kubernetes)

  




