micro list services

为什么我启动 consul agent -dev 后 在 micro list services 下始终没有 consul 服务

![](/assets/import.png)

教程上就是先启动consul 就可以在micro list services 我这个感觉 consul 和micro 没有联系上一样

原来现在新版本 默认的注册是 mdns 加上注册参数就可以了  
在运行微服务时也要加上注册方式  
micro --registry=consul list services

现在文档是基于以前的版本



