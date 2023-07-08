# mybatis-plus



#### 引入Mybatis-plus

与springboot集成
```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>${mybatis-plus-boot-starter.version}</version>
</dependency>
```

单独使用
```xml
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>mybatis-plus</artifactId>
  <version>3.5.2</version>
</dependency>
```



#### 用例

**简单的增删改查**

```java
/**
* 写一个Mapper接口，并继承 mybatis-plus的通用Mapper——BaseMapper
* 该通用Mapper提供了简单的增删改查方法
**/
@Repository
public interface MrpReturnOrderEntityMapper extends BaseMapper<MrpReturnOrderEntity> {

}
```



```java
/**
* 
*
**/
public interface BaseMapper<T> extends Mapper<T> {
    
    //插入
    int insert(T entity);
    //删除
    int deleteById(Serializable id);
	//批量删除
    int deleteBatchIds(@Param("coll") Collection<? extends Serializable> idList);
    //更新
    int updateById(@Param("et") T entity);
    //根据ID查询
    T selectById(Serializable id);
    //根据ID批量查询
    List<T> selectBatchIds(@Param("coll") Collection<? extends Serializable> idList);
    //根据查询条件查询
    List<T> selectList(@Param("ew") Wrapper<T> queryWrapper);
    //分页查询
    <E extends IPage<T>> E selectPage(E page, @Param("ew") Wrapper<T> queryWrapper);
}
```



**拼接查询语句**

```java
QueryWrapper<BalanceResultEntity> queryWrapper = new QueryWrapper<>();

//----------select子句-------------
//select name,id ...
queryWrapper.select("name","id");

//----------where子句-------------
//extGoodsCode = 123
queryWrapper.eq(BalanceResultEntity::getExtGoodsCode, "123");
queryWrapper.eq("extGoodsCode", "123");

//1 = 1
queryWrapper.eq("1", 1);

//extGoodsCode='111' and extGoodsCode='222'（默认用and连接）
queryWrapper.eq("extGoodsCode","111").eq("extGoodsCode","222");

//extGoodsCode='111' or extGoodsCode='222'
queryWrapper.eq("extGoodsCode","111").or().eq("extGoodsCode","222");

//name='John' and (extGoodsCode='111' or extGoodsCode='222')
//方法1：and
queryWrapper.eq("name","John").and(
    wrapper->wrapper.eq("extGoodsCode","111").or().eq("extGoodsCode","222"));
//方法2：nested
queryWrapper.eq("name","John").nested(
    wrapper->wrapper.eq("extGoodsCode","111").or().eq("extGoodsCode","222"));

//extGoodsCode != test
queryWrapper.ne(BalanceResultEntity::getExtGoodsCode, "test");

//extGoodsCode between 111 and 222
queryWrapper.between("extGoodsCode", "111","222");

//extGoodsCode like '%111%'
queryWrapper.like("extGoodsCode","111");

//extGoodsCode like '%111'
queryWrapper.likeLeft("extGoodsCode","111");

//extGoodsCode is null
queryWrapper.isNull("extGoodsCode");

//extGoodsCode in('111','222','333')
queryWrapper.in("extGoodsCode","111","222","333");

//id in( select id from table where id<3 )
queryWrapper.inSql("id","select id from table where id<3");

//exists(select id from table where id =10)
queryWrapper.exists("select id from table where id =10")

    
//----------order by子句-------------
//order by createTime asc
queryWrapper.orderByAsc("createTime");

//order by createTime desc
queryWrapper.orderByDesc("createTime");


//----------group by子句-------------
//group by id,name
queryWrapper.groupBy("id","name");


//----------having子句-------------
//having sum(age)>11
queryWrapper.having("sum({0})>{1}","age",11);


//----------其他-------------
//（在SQL语句最后） limit 1
queryWrapper.last("limit 1");
```



#### 拼接更新语句

```java
//update user set age=10,name='John' where id=1
public void updateUser(){
    User user = new User();
    user.setAge(10);
    user.setName("John");
    UpdateWrapper<User> wrapper = new UpdateWrapper<>();
    wrapper.eq("id",1);
    userMapper.update(user,wrapper);
}
```





**分页**

```java
//查第一页，每页10条
Page<BalanceResultEntity> entityPage = balanceResultMapper
                .selectPage(new Page<>(0, 10), queryWrapper);
```









#### 原理

**MybatisPlus初始化**

参考MybatisPlusAutoConfiguration类，略



**分页**

参考“源码解析-MybatisPlusInterceptor”可知，分页是通过**PaginationInnerInterceptor**实现的。

* 先通过下面的方法，得到查询语句返回的条数（select count(*)...）

```java
public boolean willDoQuery(Executor executor, MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException;
```

* 然后通过下面的方法，为sql拼接分页条件，再进行查询

```java
public void beforeQuery(Executor executor, MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException;
```

​	beforeQuery()拼接的原理，是看看入参中有没有传入Page对象；如果有，则按不同的数据库语法，来拼接分页条件。

​	例如：#{originalSql} 表示未拼接分页条件前的sql

​	Mysql：#{originalSql}  limit #{offset},#{limit}        ---------见MySqlDialect

​	Oracle：SELECT * FROM ( SELECT TMP.*, ROWNUM ROW_ID FROM ( #{originalSql} ) TMP WHERE ROWNUM <= #{limit} ) WHERE ROW_ID > #{offset}







#### 源码解析

**BaseMapper**











**MybatisPlusInterceptor**

全局拦截器，MybatisPlus的核心插件，自动分页、乐观锁等功能都通过该插件实现。

该插件是一个拦截器，简单说就是当发现用户要执行sql时，会先拦截下来对sql进行预处理（校验、分页、优化等等），然后再执行sql。

下面我们解析一下它的原理，建议一边参考源码一边学习。



当Mybatis需要执行以下方法时，MybatisPlusInterceptor就会拦截下来，并由自己代理执行（见MybatisPlusInterceptor的@Intercepts注解）：

* StatementHandler#prepare
* StatementHandler#getBoundSql
* Executor#update （执行更新语句）
* Executor#query （执行查询语句）



拦截后进入代理方法：

```java
public Object intercept(Invocation invocation) throws Throwable;
```



该方法通过一个拦截器集合，实现其功能

```java
private List<InnerInterceptor> interceptors = new ArrayList();
```



InnerInterceptor有以下实现：

* 自动分页：PaginationInnerInterceptor (query时拦截)
* 乐观锁：OptimisticLockerInnerInterceptor （update时拦截）
* 动态表名：DynamicTableNameInnerInterceptor  (query时拦截)
* 防止全表更新和删除：BlockAttackInnerInterceptor（update时拦截）
* sql性能规范：IllegalSQLInnerInterceptor





**IDialect**

只有一个方法，用于自动拼接分页条件。

```java
DialectModel buildPaginationSql(String originalSql, long offset, long limit);
```

支持不同数据库的分页条件拼接，包括mysql、oracle、postgresql等等（见DialectRegistry）