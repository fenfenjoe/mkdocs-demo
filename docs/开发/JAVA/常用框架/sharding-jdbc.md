# sharding-jdbc

暂时只支持mysql的分库分表（2022.10）。


### 什么是数据分片？

假设你有一个表，数据量太大了，你想将这个表里的数据分成5份，放在5个表中存储，这就叫做“数据分片”。

那怎么确定哪些数据应该存到哪个表里呢？------通过数据分片策略。

使用数据分片策略，需要你先为这个表确定出一个**“分片键”**。

分片键是表中的某个字段，分片策略会根据这个分片键来决定数据存往哪个分表。

### 有哪些数据分片策略？有什么优缺点？

可见ShardingStrategy接口，有5个实现。分别对应5种分片策略。

这5种分片策略既可用于数据库分片（DatabaseShardingStrategy），也可用于数据表分片（TableShardingStrategy）

* 标准分片策略（StandardShardingStrategy）
    只支持一个分片键，通过 (某条数据的分片键 = a) 或者 (某条数据的分片键 in(a,b,c...))来决定数据存往哪个分表。

* 复合分片策略（ComplexShardingStrategy）
    与标准分片类似，但支持多个分片键。

* 表达式分片策略（InlineShardingStrategy）
    使用Groovy的inline表达式，来表示数据存往哪个分表（比较常用）。

* Hint方式分片策略（HintShardingStrategy）

* 不分片的策略（NoneShardingStrategy）