# Druid

一款由阿里巴巴开源的数据库连接池框架。

### 参考资料

Druid wiki（中文）：[https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98](https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
SpringBoot Starter：[https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)


### 作用以及优点

* 性能优秀的数据库连接池
* 具备SQL监控、慢日志查询等功能


### 同类框架以及使用场景

* C3P0
* DBCP
* BoneCP


### 在SpringBoot APP中配置

添加maven依赖
```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
```


添加yaml配置
```yaml
#druid控制台的配置
spring:
  druid:
    #IP黑名单
    deny:
    #IP白名单
    allow:
    loginUsername: admin
    loginPassword: 123456

#druid数据库连接池配置
druid:
  #配置初始化大小、最小、最大线程数
  initialSize: 5    
  minIdle: 5
  #CPU核数+1，也可以大些但不要超过20，数据库加锁时连接过多性能下降
  maxActive: 20
  #最大等待时间，内网：800，外网：1200（三次握手1s）
  maxWait: 60000
  #配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
  filters: stat,wall,log4j
  #保持长连接
  keepAlive: true
  maxPoolPreparedStatementPerConnectionSize: 20
  useGlobalDataSourceStat: true
  #mergeSql=true
  #slowSqlMillis
  #log.conn
  #log.stmt
  #log.rs
  #stmt.executableSql 是否打印可执行sql到日志
  connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000;druid.log.conn=false;druid.log.stmt=true;druid.log.rs=false;druid.log.stmt.executableSql=true

```



### 在控制台查看SQL执行情况

(均为微服务的IP、端口号)
http://xx.xx.xx.xx:xxxx/yourapp/druid/index.html