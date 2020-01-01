# Web管理控制台

## 运行

```
micro web
```

使用浏览器打开[http://localhost:8082](http://localhost:8082)

使用ACME协议

Micro Web支持Let's Encrypt开发的ACME协议，它可以帮你的域名颁发证书。

micro --enable\_acme web

可以指定白名单：

micro --enable\_acme --acme\_hosts=example.com,api.example.com web

设置TLS证书

管理控制台也支持使用TLS证书

micro --enable\_tls --tls\_cert\_file=/path/to/cert --tls\_key\_file=/path/to/key web

相关截屏

下面是Web运行起来访问的截屏：

![](/assets/import3456789.png)

![](/assets/impor1t.png)

