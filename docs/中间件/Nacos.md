 # Nacos

### Nacos是CP模式还是AP模式？

CP、AP均支持。可以互相切换。

Nacos集群默认是AP模式。

### 何时用CP模式，何时用AP模式？

AP模式：Spring Cloud服务、Dubbo服务

CP模式：K8S服务、 DNS服务

### Nacos常见概念
**服务（service）**
nacos支持多种类型的服务（service）的发现、配置及管理：
* K8S Service
* gRPC & Dubbo RPC Service
* Spring Cloud Restful Service

**配置集（config）**
一个配置文件就是一个配置集。每个配置集都有一个唯一标识号（DATA ID）；
配置集支持多种格式。常用的格式是.properties 和.yml；


**命名空间（namespace）**
服务（Service）和配置（Config）通过命名空间来区分。

命名空间用于区分不同租户和不同环境。
比如，为不同供应商定义不同的命名空间；
比如，为开发环境、预发布环境、正式环境定义不同的命名空间；

默认命名空间为public。

**配置分组（group）**
同一命名空间中的配置（Config），还可以通过配置分组（group）来区分。

配置分组的常见场景：不同的应用或组件使用了相同的配置类型，如 database_url 配置和 MQ_topic 配置。

默认的组为DEFAULT_GROUP。



**服务集群（cluster）**
一般可以根据服务所在的网络分区，为服务实例分组。
比如，可以将服务实例分成广州集群、上海集群。

默认的集群为DEFAULT。

>注意要将“服务集群”与“注册中心集群”区分开来。

### 单机版Nacos的特征？
单机模式下，Nacos默认将服务和配置信息保存到内嵌的数据库中（Derby）。

如果要改为集群模式，当然不能用某台机器上的Derby去保存服务和配置信息。

因此，0.7版本之后，增加了对Mysql数据源的支持，即服务和配置信息可保存到Mysql中。


### 如何搭建Nacos注册中心集群？

