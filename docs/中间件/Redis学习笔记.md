 # Redis速查宝典

[toc]
##### 常用Redis可视化客户端

* RedisView
* Redis Desktop Manager （收费）

##### Redis的数据类型

* string（字符串）
* hash（哈希表）
* list（双向链表）
* set（集合）
* zset(sorted set：有序集合)

##### Redis小知识

* Redis的初始密码为空；
* Redis有默认的16个数据库（又称“字典”）；
* Redis数据库以db0,db1...db15命名，不支持自定义名称；
##### Redis持久化
有两种方案。

###### RDB（默认策略）
RDB 持久化方式，是将 Redis 某一时刻的数据持久化到磁盘中，是一种快照式的持久化方
法。

优点：
* 记录了某一时刻的数据，相当于数据备份；
* RDB会对数据经过压缩，方便存储、传输，适合灾难恢复；

缺点：
* 数据量大时，通过RDB恢复数据非常耗时，且会阻塞主进程，恢复时Redis不能响应客户端的请求。

###### AOF
AOF 方式是将执行过的写指令记录下来，在数据恢复时按照从前到后的顺序再将指令都执行
一遍。

优点：
* 相当于“指令的日志”，易读。


缺点：
* 一般比RDB文件要大

##### 常见Redis集群架构模式

三种模式：
- 主从模式 最低配置：1主1从
- 哨兵模式 最低配置：1主2从3哨兵
- 集群模式 最低配置：3主3从


###### Redis master/slave（主从复制模式）
* 最简单的集群方式。满足高性能、但不满足高可用。
* 主从模式节点：
    1. master节点：可处理读、写请求
    2. slave节点：只可处理读请求
*  如何搭建？
    只要在slave节点的配置文件中配置好master的ip及端口即可。
* 优点
    1. 数据冗余
    2. 读写分离
    3. 故障恢复
* 缺点
     1. master节点都宕机后，需要人工将从节点晋升成master节点（改配置）
     2. master、slave保存的都是同样的数据，做不了数据分片
     3. 读压力可以通过增加slave来缓解，但因为只能有一台mastrer，写压力难以降低
* 适用场景
###### Redis sentinel（哨兵模式）
* 在主从模式的基础上，增加了哨兵集群。满足高性能、但不满足高可用。
* 哨兵集群负责监测master和slave节点的运行状态。当master节点宕机时，哨兵集群负责从slave节点里选出新的master节点。
* 哨兵模式节点：
    1. master节点
    2. slave节点
    3. sentinel(哨兵)节点
* 概念
    * 主观下线：当某个sentinel节点向master节点发送ping时，超过某个时间（own-after-milliseconds）没有回应，那么该sentinel节点便会认为master节点宕机了，即“主观下线”。
    * 客观下线：当有quorum个sentinel节点，认为master节点宕机（主观下线）了，那么该sentinel集群便承认master节点确实宕机了，即“客观下线”。客观下线后，sentinel集群会选出一个sentinel节点，来负责从slave节点中重新选出新的master节点。
    *  epoch：类似于版本号，sentinel会为每个新的Master节点生成一个epoch作为标记。
* 原理
    1.  ***sentinel节点是如何运作的***
        每个sentinel节点，会跑三个定时任务：
        1. 心跳检测：每隔1s向master节点发送ping信号
        2. 信息交换：每隔2s向master节点的chanel发送自己掌握的一些集群信息，以及自己的信息（host、ip、run id等）
        3. 发现最新的集群拓扑结构：每隔10s向master和slave发送INFO命令
    2. ***客观下线的过程***
        1. 当某个sentinel节点（称为sentinel A）主观下线后，便会向其他sentinel节点发送以下消息：
        ```bash
            SENTINEL is-master-down-by-addr ip port current_epoch runid
            # ip 自己的ip
            # port 自己的端口
            # current_epoch master的当前版本号
            # runid 此时为*
        ```
        2. 其他sentinel节点收到消息后，返回自己对master的检测结果
        ```bash      
            down_state（1表示已下线，0表示未下线）
            leader_runid（领头sentinal id，仅竞选领头sentinel时用到）
            leader_epoch（领头sentinel纪元，仅竞选领头sentinel时用到）
        ```
        3. sentinel A收到回复后，判断主观下线的数量是否达到quorum的值，若达到，则认为master客观下线
    3. ***客观下线后，sentinel集群如何选择领头sentinel（负责重新选master节点的sentinel节点）***
        1. 接上（客观下线的过程），sentinel A客观下线后，sentinel集群需要重新选master节点
        2. sentinel A向其他sentinel节点发送以下消息：
        ```bash
            SENTINEL is-master-down-by-addr ip port current_epoch runid
            # ip 自己的ip
            # port 自己的端口
            # current_epoch master的当前版本号
            # runid 此时的runid为自己的runid，每个sentinel节点都有自己的runid
        ```
        3. 其他sentinel节点在收到消息后，会选择第一个给自己发消息的作为领头sentinel。
        4. 其他sentinel节点在收到消息后，都会回复以下消息。
        ```bash    
            down_state（1表示已下线，0表示未下线）
            leader_runid（自己选的领头sentinal runid）
            leader_epoch（自己选的领头sentinel纪元）
        ```
        5. sentinel A在收到所有回复后，判断选自己的数量是否超过majority参数，若超过则自己是领头节点。

     4. ***领头节点如何处理故障转移***
        1. 按一定算法给每台slave定个优先级，优先级高的当选master；
        2. 向其他slave发送slaveof命令，通知其他slave新的master

* 优点
    1. 主从模式的优点，哨兵模式都有；
    2. 相比主从模式，主节点宕机后，会自动做故障发现及转移（从slave节点里选举出新的master节点）；
* 缺点
    1. 主从切换时，集群系统不可用；
    2. 主从模式的缺点，哨兵模式也基本都有；

###### Redis Cluster
* 什么时候考虑使用Redis Cluster？
    1. 需要更高的QPS（主从模式下QPS全看master的性能）
    2. 有更大的数据量（主从模式不能做数据分片，因为主、从节点存储的数据都是一样的）
* 原理
    Redis Cluster下的每个节点都负责进行读写操作；
    Redis Cluster通过虚拟槽的方式去做数据分片；
* 优点
    支持数据分片；
    可以提高写请求性能；
    
* 数据分片的方法？
    1. 顺序分布
    2. 哈希分布
        * 节点取余分区
        * 一致性哈希分区
        * 虚拟槽分区（Redis cluster使用该方法，一共16383个虚拟槽）

* Redis Cluster & 虚拟槽分区
  Redis Cluster准备了16383个虚拟槽，用于存储集群的所有数据，这些虚拟槽分布在所有的Redis Master机器上。
  假设现在，需要对集群写入一条数据，比如（SET my_key my_value）
  1. 首先，我们需要知道，将数据存入哪个虚拟槽。
     通过对key按CRC16规则进行hash运算，得到一个hash值；
  2. 把hash结果对16383进行取余
  3. 根据求的余数，存到负责该虚拟槽的redis节点处



##### Redis的使用场景

数据库、缓存、消息中间件

* key-value：
    1. 分布式session（SET key value）
    2. 全局计数器（INCR key）
    3. 全局ID生成器（INCR key）
    4. 对象存储（如有对象 {name:"dyz",id=1} ）
        * 方法一 ，分字段存储：mset obj:1:name dyz obj:1:id 1）#等于在obj表存了一个id为1的对象，这种方式方便修改
        * 方法二，json字符串存储 mset obj:1 "{name:'dyz',id=1}" #等于在obj表存了一个id为1的对象，这种方式方便存储
    5. 分布式锁（SETNX）
        * 获取锁：
        ```bash
        #NX:已有该锁则返回false,否则true
        #PX 30000：设置过期时间为30s
        SET key value NX PX 30000 
        ```
        * 释放锁：
        ```bash
        #需要通过Lua脚本实现，确保原子性，伪代码如下：
        #如果锁是这个客户端生成的，才允许释放
        if redis.get(lock) == $uuid
          redis.del(lock)
        ```
        > 如果是通过Java调用Redis分布式锁，则已经有完善的实现：Redisson
* list：
    1. 消息队列
    ```bash
    #生产消息
    LPUSH key value
    #消费消息
    brpop key
    ```
* hash：
    1. 对象存储，可对应很多种业务（购物车等）
        * 方法一 ，分字段存储：hmset obj 1:name dyz 1:id 1）#等于在obj表存了一个id为1的对象，这种方式方便修改
        * 方法二，json字符串存储 hset obj 1 "{name:'dyz',id=1}" #等于在obj表存了一个id为1的对象，这种方式方便存储
* set：
    1. 小程序抽奖（参与抽奖SADD、查看所有抽奖用户SMEMBERS、抽取N名中奖者SRANDMEMBER）
    2. 动态点赞（点赞SADD like:{消息ID} 用户ID；取消点赞SREM like:{消息ID} 用户ID）
    3. 关注列表（关注SADD guanzhu:{用户ID} 用户ID；取关SREM guanzhu:{用户ID} 用户ID）
* zset：
    1. 排行榜（添加进排行榜zadd rankset 1 dyz 2 dyz2；获取排行榜前10名 ZRANGE rankset 0 9）

##### Redis的过期机制
Redis在以下两种情况下会自动清理Key：
1. 设置了过期时间的key到期了，Redis会对其进行清理
2. 通过淘汰策略删除key
> 过期了之后具体是怎么清理的？

1. 定期检查：Redis会定期检查**部分** 设置了过期时间的key；
2. 惰性检查：用户查询某个key时，redis会检查一下是否设置了过期时间，是否有过期；

>如果没被“定期检查”、“惰性检查”删除，redis的内存就会积压很多过期key，这问题怎么解决？

通过Redis的淘汰策略。

##### Redis的淘汰策略
###### 什么是淘汰策略？

Redis 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。

###### 有哪些淘汰策略？
* noeviction: 不删除策略, 达到最大内存限制时, 如果需要更多内存, 直接返回错误信息。 大多数写命令都会导致占用更多的内存(有极少数会例外, 如 DEL )。
* allkeys-lru: 所有key通用; 优先删除最近最少使用(less recently used ,LRU) 的 key。（这个是最常用的）
* volatile-lru: 只限于设置了 expire 的部分; 优先删除最近最少使用(less recently used ,LRU) 的 key。
* allkeys-random: 所有key通用; 随机删除一部分 key。
* volatile-random: 只限于设置了 expire 的部分; 随机删除一部分 key。
* volatile-ttl: 只限于设置了 expire 的部分; 优先删除剩余时间(time to live,TTL) 短的key。

##### Redis的语法

###### key（键）
	DEL <key>
	EXISTS <key>
	【设置过期时间（几秒后）】EXPIRES <key> <seconds>
	【设置过期时间（时间点）】EXPIREAT <key> <timestamp>
	【根据给定模式匹配key】Keys <pattern>
###### string（键值对）
	SET <key> <value> 
    GET <key>
    
    SET name dyz #单值缓存
    GET name #单值获取，返回dyz
    
    MSET dyz_obj:1:name dyz dyz_obj:1:id 1 #json对象缓存 
    GET dyz_obj:1:name #获取对象属性，返回dyz
    
    【批量添加】MSET <key> <value> <key> <value>
    【批量获取】MGET <key> <key>
    【若key不存在，才存入该键值对】 SETNX <key> <value> #常用于分布式锁
	【返回Value的长度】STRLEN <key>
	【追加字符串】APPEND <key> <value>
	【返回子字符串】GETRANGE <key> <start> <end>
	【置新值，返回旧值】GETSET <key> <value>
###### hash（哈希表）
    HSET <hashname> <key> <value>
	【批量添加】HMSET <hashname> <key1> <value1> <key2> <value2> ... 
	HGET <hashname> <key>
	【返回所有键值对】HGETALL <hashname>
	【返回所有键】HKEYS <hashname>
	【返回所有值】HVALS <hashname>
	【返回键值对的数量】HLEN <hashname>
    【移除某个键值对】HDEL <hashname> <key>
###### list（双向链表）
    【将给定值推入到列表左端】LPUSH key value1 value2 ...
    【从列表的左端弹出一个值，并返回被弹出的值】LPOP key
    【通过索引获取列表中的元素。你也可以使用负数下标】LINDEX key index
    【获取列表在给定范围上的所有值】LRANGE key startindex endindex
    【当列表没有值时，阻塞等待，直至有值可取时才继续执行】 BRPOP key
    #lrange mylist 0 -1 返回列表所有值
    #lpush+lpop=Stack(栈)
    #lpush+rpop=Queue（队列）
    #lpush+ltrim=Capped Collection（有限集合）
    #lpush+brpop=Message Queue（消息队列）
###### set（集合）
	【往集合中添加成员】sadd <setname> <key>
	【返回集合中所有成员】smembers <setname>
    【获取集合size】SCARD key
    【判断 member 元素是否是集合 key 的成员】SISMEMBER key member
    【从集合中随机抽取n个成员】 SRANDMEMBER key n
    【从集合中随机抽取n个成员并删除】 SPOP key n
    【交集】SINTER set1 set2 set3
    【并集】SUNION set1 set2 set3
    【差集】SDIFF set1 set2 set3  #第一个集合的元素减去剩余集合的并集的元素
###### zset（有序集合）
	【往集合添加成员，并标记该成员的分值】zadd <setname> <score> <key> <score> <key> ...
    【获取集合在给定范围上的所有值】ZRANGE key startindex endindex
    【删除某个成员】ZREM zset-key member1
###### bitmap（2.2.0版本新增）
    【将某个bitmap的第offset位设置位0或者1】setBit key <offset> <value(0或者1)>
    【取某个bitmap的第offset位得值】getBit key <offset>
    【返回指定区间里，某个bitmap位为1的个数】bitCount key [start] [end]
> bitmap不是数据类型，实际上它是通过字符串来实现的。
> 在redis中，一个字符串最大可占用512MB，512MB=2^9MB=2^19KB=2^29B=2^32bit
> 因此，理论上bitmap的offset最大可以存储到2^32（4 294 967 296，42亿+）


##### Redis会遇到的问题
###### 1.缓存雪崩
缓存雪崩是指，如果所有key的失效时间一致，便会造成在某一时间段所有key失效；此时查询请求会直接去到数据库中。若请求太多甚至会使数据库挂掉。
处理方法：为key的失效时间加个随机值。

###### 2.缓存穿透
缓存穿透是指，用户不断查询缓存中没有的数据，导致请求直接去到数据库中。
处理方法：
1.增加参数校验，不符合规格的入参直接返回报错。
2.Redis自带的布隆过滤器。

###### 3.缓存击穿
与缓存雪崩类似，缓存击穿是有一个Key并发特别大，当该key失效时，大量的请求直接去到数据库，拖垮数据库的性能。
处理方法：
1.设置热点数据永远不过期。
2.为KEY的更新操作加上互斥锁。

##### Redis的运维
###### Redis配置文件（redis.conf）
```bash
port 6379  #端口号，默认为6379
dbfilename dump.rdb #数据库文件名，默认为dump.rdb
slaveof <masterip> <masterport> #主从模式下，当本机为slave时，设置master的端口和ip
replicaof <masterip> <masterport> #Redis5后用来代替slaveof
masterauth <master-password> # 当master有密码保护时，需要配置
appendonly no # 开启AOF持久化，默认不开启AOF，用的是RDB持久化
logfile "/usr/software/redis/redis-ms/7001/log/redis.log" # 日志文件输出路径
pidfile /var/run/redis_7002.pid # pid文件路径
requirepass 123456 #密码
rdbcompression yes #存储至文件系统时是否压缩文件，默认为yes
dir ./ #本地数据存放位置
#RDB持久化会自动备份，默认备份规则如下：
save 900 1 #900秒里面发生1次操作则执行一次save。
save 300 10 #300秒了发生了10次操作则执行一次save
save 60 10000 #60秒里面发生了1万次操作则执行一次save。
```

###### 启动Redis
```bash
redis-server config/redis.conf #启动后自动连接服务端
```
###### 连接Redis
```bash
redis-cli #连接本地redis服务端，端口为默认的6379
redis-cli -h 192.168.20.33 -p 6380 #连接远程redis服务端，端口为6380
```
###### 在Redis客户端内查看当前配置
```bash
#查看所有配置
CONFIG GET *
#查看端口
CONFIG GET port
```

###### 搭建Redis集群（主从模式）
***方式一：***
* 创建master-6379.conf
```conf
port 6379
dbfilename dump-6379.rdb
logfile ../log/redis-6379.log
pidfile redis-6379.pid
masterauth 123456
requirepass 123456
```
* 创建slave-6380.conf
```conf
port 6380
dbfilename dump-6380.rdb
logfile ../log/redis-6380.log
pidfile redis-6380.pid
masterauth 123456
replicaof localhost 6379
```
* 启动主节点、从节点
```conf
redis-server config/master-6379.conf
redis-server config/slave-6380.conf
```
***方式二：***
* 创建redis-6379.conf、redis-6380.conf（不配置replicaof）
* 启动两个Redis
```conf
redis-server config/redis-6379.conf
redis-server config/redis-6380.conf
```
* 进入6380 Redis
```bash
redis-cli -p 6380
```
* 将6379设为6380的主节点
```bash
127.0.0.1:6380> slaveof localhost 6379
```
* 取消主节点的关联
```bash
127.0.0.1:6380> slaveof no one
```
###### 查看主从状态
```bash
127.0.0.1:6380> info replication
```
###### 搭建Redis集群（哨兵模式）
* 写哨兵节点的配置（sentinel.conf）
```bash

port 26379 #sentinel实例运行的端口 默认26379
dir /tmp #哨兵sentinel的工作目录
sentinel monitor mymaster 127.0.0.1 6379 2 #表明自己是一个哨兵 且主节点名称：mymaster ip 端口 至少2个哨兵判断master节点失效时，master才会被标记为下线
```
* 用哨兵模式启动Redis集群
方式一：
```bash
redis-sentinel /path/to/sentinel.conf
```
方式二：
```bash
redis-server /path/to/sentinel.conf --sentinel
```
###### 搭建Redis集群（cluster模式）

* 写配置文件（redis-6379.conf）
```bash
port 6379
dbfilename dump-6379.rdb
logfile ../log/redis-6379.log
pidfile redis-6379.pid
dir ./6379/ #本redis节点数据文件存储位置
cluster-enabled yes #启动集群模式
cluster-config-file nodes-6379.conf #集群节点信息文件
cluster-node-timeout 5000 # 节点离线的超时时间
#去掉bind绑定访问ip信息
#bind 127.0.0.1
appendonly yes #启动AOF文件
#设置redis访问密码
requirepass redis-pw
#设置集群节点间访问密码，跟上面一致
masterauth redis-pw
```
* 拷贝该份redis-6379.conf到其他机子，并修改一下端口号（6380、6381、6382...）
* 启动redis服务端
```bash
redis-server redis-6379.conf
redis-server redis-6380.conf
redis-server redis-6381.conf
...
```
* 检查是否启动成功
```bash
ps -ef|grep redis
```
* 使用redis-cli搭建整个集群
```bash
redis-cli -a redis-pw --cluster create --cluster-replicas 1 192.168.1.1:6379 192.168.1.1:6380 192.168.1.1:6381 192.168.1.2:6379 192.168.1.2:6380 192.168.1.2:6381 
#-a ：密码；　　 
#--cluster-replicas 1：表示1个master下挂1个slave； --cluster-replicas 2：表示1个master下挂2个slave
# 上述脚本等于部署了3个master，每个master下有一个slave：
#192.168.1.1:6379（主）192.168.1.2:6379（从）
#192.168.1.1:6380（主）192.168.1.2:6380（从）
#192.168.1.1:6381（主）192.168.1.2:6381（从）
```

* 登录集群（任意一个客户端）
```bash
redis-cli -a redis-pw -c -h 192.168.1.1 -p 6379
#‐a表示服务端密码；
#‐c表示集群模式；
#-h指定ip地址；
#-p表示端口号
```

* 查看集群信息
```bash
cluster info
# 显示总节点数（master+slave）、master节点数等信息
```
* 查看集群节点的信息
```bash
cluster nodes
```
* 关闭集群中的节点
```bash
redis‐cli ‐a redis-pw ‐c ‐h 192.168.1.1 ‐p 6379 shutdown
# 需要逐台关闭
```

##### Redis的原理
1. Redis并发方面的设计与其他数据库类比

||Redis|Memcache|Mysql|Oracle|
|---|---|---|---|---|
|IO处理方式|多路复用|多路复用|多路复用?|
|并发方式|单线程|多线程|多线程(默认CPU核心数)thread_pool_size|多进程（Linux）、单进程多线程（Windows）|
|数据存储位置|内存|内存|硬盘|硬盘|
|可持久化|支持|不支持|支持|支持|
|QPS(每秒查询数)|10w+(连接数少于1w时)|||


2. 怎么理解Redis的单线程模式
Redis通过多路复用IO的方式，监听多个套接字；
将返回的已就绪的套接字放进队列，并以有序、同步的方式，将这些需要处理的套接字移交给事件处理器处理。因为只会开一个线程去执行事件，因此不会出现并发的问题。

3. 为什么要用单线程模式
多线程适用于执行操作会与硬盘有交互的数据库（比如常见的oracle、mysql），这些数据库在运行执行计划时，都需要将数据从硬盘缓存到内存中（IO操作）；
但是Redis“在执行时”不需要访问硬盘，使用单线程模式，可以避免线程切换、线程同步所带来的额外的开销。

##### Redis FAQ
###### Redis如何与数据库的数据保持一致性？
**Cache Aside Pattern（最常见的策略）**
查询：先查缓存，缓存没有则查数据库；数据库有，则更新到缓存并返回；数据库没有，则返回没有。
更新：先更新数据库，再将缓存中的置为失效。

##### 参考资料

redis哨兵模式原理 [https://www.cnblogs.com/gunduzi/p/13160448.html](https://www.cnblogs.com/gunduzi/p/13160448.html)

redis命令手册 [https://www.redis.net.cn/order/](https://www.redis.net.cn/order/)

