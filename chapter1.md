# chapter1

实现一个简单的基于go-micro的微服务。

  


为简单起见，client与server之间我们使用点对点的同步方式（Transport），即无需消息中间件（Broker），注册中心采用consul系统。

