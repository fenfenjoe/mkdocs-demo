# apollo



#### Apollo控制台

通过 localhost:8070 访问。



常见概念：

**部门**

公司内，每个行政部门会拥有自己开发的应用；部门成员只能访问属性本部门的应用。



**应用（app）**

在Apollo上，可管理多个应用的配置（key-value）。



**环境（env）**

在Apollo上，可管理同一个应用不同环境下的配置。一般有以下环境：

* DEV（开发环境）
* SIT（联调环境）
* UAT（测试环境）
* PET（压测环境）
* VER（正式环境）



**集群（cluster）**

就算在同一环境下，应用也可能部署在不同的机房，在这集群的概念指的就是机房。

若在不同机房的配置需要不一样，则可为应用添加不同集群的配置。



**命名空间（namespace）**

还可以根据配置的用途，将配置（key-value）分类到不同的命名空间中。

比如，默认的命名空间application，负责保存通用的配置（app名称、端口等），对应application.properties；

新增一个命名空间db，负责保存数据库配置，对应db.properties；

新增一个命名空间spring，负责保存spring相关配置，对应spring.properties；

【private】：只有本项目可用该配置

【public】：其他项目可继承该配置



**实例**

即已部署的应用，在控制台中可以查看某个配置有哪些实例在依赖。





#### Apollo控制台常用操作

**发布配置**



**删除配置**



**批量添加配置**



**创建通用配置的命名空间**



**创建新的命名空间，并关联通用配置的命名空间**







#### SpringBoot 整合Apollo



**maven.xml**

```xml

<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-client</artifactId>
    <version>1.7.0.m7.RELEASE</version>
</dependency>
<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-core</artifactId>
    <version>1.7.0.m7.RELEASE</version>
</dependency>

```



**application.properties**

```properties

#Apollo配置中心地址
apollo.meta=xxx 
#是否开启Apollo
apollo.bootstrap.enabled=true
#配置缓存路径
apollo.cache-dir=/opt/data/cache-dir
#命名空间（会读取[命名空间].properties的配置）
apollo.bootstrap.namespaces= application
#是否系统初始化前就加载配置（若日志的配置放到APOLLO，需要开启）
apollo.bootstrap.eagerLoad.enabled= true
#指定使用某个集群下的配置
apollo.cluster=xxx
#Apollo上配置更新后，是否自动同步到应用中
apollo.autoUpdateInjectedSpringProperties=true

#项目名称
app.id=xxx
#项目环境
apollo.environment=dev
```







#### 客户端加载Apollo配置原理

在为我们的SpringBoot APP添加Apollo配置时，会将APP与Apollo上某个环境下（apollo.environment）的某个项目（app.id）绑定；



通过下面的方法可以读取Apollo的配置

```java

Config appConfig = ConfigService.getAppConfig(); //读取默认命名空间application的配置
appConfig.getProperty("me.name"); //读取key为me.name

Config appConfig = ConfigService.getConfig("db"); //读取命名空间db的配置
appConfig.getProperty("db.url");//读取key为db.url
```





当用户在控制台更新了配置时，Apollo会将新的配置异步推送给客户端（App）；

而客户端也会每隔5分钟，向Apollo拉去最新的配置信息。







#### Apollo的灰度发布

灰度发布，即只让某一部分的服务器上线新的配置，观察一段时间后没问题后，才全量发布新的配置。

使用场景：

* 系统调优



