# Spring Cloud Alibaba

### 简介

### 与Spring Cloud 组件类比

|功能|Alibaba| Netflix|其他|
|---|---|---|---|
|注册中心、配置中心|Nacos|Eureka + Config + Admin|Consul、CoreDNS、Zookeeper|
|服务熔断、降级|Sentinel | Hystrix + Dashboard + Turbine|
|服务提供|Dubbo | Ribbon + Feign
|消息中心|RocketMQ | RabbitMQ
|轮询|Schedulerx | Quartz
|网关|| Gateway/Zuul
|链路跟踪||Sleuth +Zipkin



#### Nacos：注册中心、配置中心

##### 下载、安装
项目（下载）地址：[https://github.com/alibaba/nacos](https://github.com/alibaba/nacos)


##### 启动Nacos注册中心

**单机版：**

Linux：
```bash
sh bin/startup.sh -m standalone
```
Windows：
```bash
cmd bin/startup.cmd -m standalone
```

在浏览器访问：http://localhost:8848/nacos/index.html便可进入控制台。

**集群版：**
1. 修改配置文件
```bash
# conf/cluster.conf
#填入所有要运行Nacos Server的机器IP
192.168.100.155 
192.168.100.156
```

```bash
# conf/application.properties
# 填入mysql的信息
db.num=1 
db.url.0=jdbc:mysql://localhost:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true 
db.user=root 
db.password=root
```

2. 创建一个名为nacos_config的 database，将NACOS_PATH/conf/nacos-mysql.sql中的表结构导入刚才创建的库中


3. 在浏览器访问：http://localhost:8848/nacos/index.html便可进入控制台。


##### nacos的spring-cloud配置参数

**discovery（注册中心）**
参考：https://github.com/alibaba/spring-cloud-alibaba/wiki/Nacos-discovery

**config（配置中心）**
参考：
#### Sentinel：熔断降级

#### Seata：分布式事务

#### Dubbo：服务调用

#### OSS（Object Storage Service）：对象存储

#### SideCar：