### 运行Hello服务 {#item-3-7}

启动consul之后  
执行`micro list services`查看当前已有服务：

```
>
 micro 
list
 services
consul
```

执行`go run services/hello.go`命令，启动hellooo服务：

```
>
go
run
services
/
hello
.go
2018
/
11
/
29
20
:18
:08
Listening
on
[::]
:61463
2018
/
11
/
29
20
:18
:08
Broker
Listening
on
[::]
:61464
2018
/
11
/
29
20
:18
:08
Registering
node
: 
hellooo-74122f56-4728-4449-a9d4-6c3c85ba2fcb

....
```

再次执行`micro list services`查看当前已有服务：

```
>
 micro 
list
 services
consul
hellooo
```

即hellooo服务已启动

**注**通过WebUI来查看各service信息：[http://localhost](http://localhost/):8500/

请求服务  
执行`go run clients/helloclient.go`命令，向hellooo服务发起请求：

```
>
go 
run
 clients/helloclient.go

Hello World ^_^
```

## References {#item-4}

[https://github.com/micro/go-m...](https://github.com/micro/go-micro)  
[https://micro.mu/docs/go-micr...](https://micro.mu/docs/go-micro-architecture.html#)  
[https://lixiangyun.gitbooks.i...](https://lixiangyun.gitbooks.io/go-micro/content/)  
[https://github.com/hb-go/micro](https://github.com/hb-go/micro)



