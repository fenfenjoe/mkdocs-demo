# Tomcat学习笔记


### Tomcat的三种并发模式

* BIO
* NIO
* APR


#### NIO

原理：

* Acceptor线程：1个线程，负责接收所有连接（socket），并存放到PollerEvent队列中
* PollerEvent队列
* Poller线程：1~2个线程，负责从PollerEvent队列中取出socket，并注册到自己的selector中
* Worker线程：默认是10个，默认最大200个线程；真正处理请求的线程



### SpringBoot是如何启动内嵌的Tomcat的？

可见：ServletWebServerApplicationContext.createWebServer()方法