# Web管理控制台

## 运行

```
micro web
```

使用浏览器打开http://localhost:8082



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



作者：承诺一时的华丽

链接：https://www.jianshu.com/p/f2bb2cd7d0a9

来源：简书

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

