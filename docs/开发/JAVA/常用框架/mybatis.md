# mybatis 

与Springboot集成
```xml
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>3.0.0</version>
</dependency>
```

单独使用
```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.5.11</version>
</dependency>
```
### Mybatis结构

* SqlSessionFactoryBuilder ----Mybatis的入口，用来加载mybatis-config.xml，并创建SqlSessionFactory

* SqlSessionFactory ----用来创建SqlSession

* SqlSession ----提供了select、update、insert、开启事务等的方法；将需要的参数全封装到MappedStatement中

* Executor ----根据SqlSession的传参，调用StatementHandler执行请求
* StatementHandler ----执行SQL的过程
* ResultSetHandler ----SQL结果组装的过程
* ParameterHandler ----SQL参数组装的过程

> 调用上面4大对象前，都会先执行interceptorChain.pluginAll()，调用插件（Inteceptors）；可见SimpleExecutor

* MappedStatement ----执行SQL全过程需要的参数均放这里
  * SqlSource
    * BoundSql


### 拦截器



#### 拦截器的使用场景

* 查询前自动分页
* 结果集敏感数据处理
* 打印sql以及执行时间


#### 如何自定义拦截器

可以参考mybatis-plus是怎么通过拦截器实现分页的。（MybatisPlusConfig.class）

```java
@Intercepts({@Signature(
    type = StatementHandler.class,
    method = "prepare",
    args = {Connection.class, Integer.class}
)})
public class PaginationInterceptor implements Interceptor {
    //...
}
```

### 分页

使用PageHelper & PageInfo



### SpringBoot启动时，Mybatis做了什么
当你依赖了mybatis-spring-boot-starter后:
1. mybatis-spring-boot-starter会为你依赖mybatis需要的包，例如：
* spring-boot-starter
* spring-boot-starter-jdbc
* mybatis
* mybatis-spring

2. mybatis-spring-boot-starter还会依赖一个mybatis-spring-boot-autoconfigure包，里面放着一些自动配置

见mybatis-spring-boot-autoconfigure/META-INF/spring.factories；  
当SpringBoot启动时，会自动加载MybatisAutoConfiguration类。  
下面我们解读一下这个自动配置类。  

```java
@Configuration //表明这是一个自动配置类
@ConditionalOnClass({SqlSessionFactory.class, SqlSessionFactoryBean.class}) //当有这两个类时，才加载当前这个配置类
@ConditionalOnBean({DataSource.class}) //当有Datasource的实例对象时，才加载当前这个配置类
@EnableConfigurationProperties({MybatisProperties.class}) //加载MybatisProperties的实例对象
@AutoConfigureAfter({DataSourceAutoConfiguration.class}) //加载完DataSourceAutoConfiguration后再加载当前配置类
public class MybatisAutoConfiguration {
    private static final Logger logger = LoggerFactory.getLogger(MybatisAutoConfiguration.class);
    private final MybatisProperties properties;
    private final Interceptor[] interceptors;
    private final ResourceLoader resourceLoader;
    private final DatabaseIdProvider databaseIdProvider;
    private final List<ConfigurationCustomizer> configurationCustomizers;

    //看是否需要加载用户自定义的mybatis-config.xml文件
    @PostConstruct
    public void checkConfigFileExists() { //...}

    //没有该实例则创建
    @Bean
    @ConditionalOnMissingBean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception { //... }

    //没有该实例则创建
    @Bean
    @ConditionalOnMissingBean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) { //... }


    @Configuration //又一个自动配置类
    @Import({AutoConfiguredMapperScannerRegistrar.class}) //加载下面的AutoConfiguredMapperScannerRegistrar的实例对象
    @ConditionalOnMissingBean({MapperFactoryBean.class})
    public static class MapperScannerRegistrarNotFoundConfiguration { //... }

    //该类负责扫描BasePackage下的所有带@Mapper的接口，创建它们的实例对象，并加载到Context中
    public static class AutoConfiguredMapperScannerRegistrar implements BeanFactoryAware,
     ImportBeanDefinitionRegistrar, ResourceLoaderAware {
        private BeanFactory beanFactory;
        private ResourceLoader resourceLoader;

        //...
    }
}

```

> 可以看出，Spring启动时，主要加载了：1.mybatis的config配置 2.用户自定义的mapper类；  
> 我们一般使用@MapperScan(basePackage="")来扫描Mapper接口；用了@MapperScan便不会走自动配置中扫描的逻辑；  


### Mapper调用原理

在Mybatis中，只需要在Service层定义一个Mapper接口，然后autowired一下，就可以使用Mapper接口执行相应的sql； 
它为我们省去了很多操作，包括：
* 获取数据库连接
* 执行SQL
* ResultSet转换
* 关闭数据库连接
那它的原理是怎样的呢？ 

我们可以从@MapperScan这个注解入手。因为我们的Mapper实例都是通过这个注解扫描进IOC容器中，并注入到Service层中的。

通过下面的调用过程，我们可以知道注入Mapper的对象究竟是什么。  

@MapperScan -> MapperScannerRegistrar -> ClassPathMapperScanner -> MapperFactoryBean ->
SqlSession -> SqlSessionTemplate -> SqlSessionFactory -> Configuration -> MapperRegistry -> 
MapperProxyFactory -> MapperProxy 

最后注入Mapper的实际是一个MapperProxy实例。  
那么Mapper对象是如何执行SQL，组装结果并返回的呢？  
继续看MapperProxy.invoke()方法。

mapperMethod.execute() -> executeForMany() -> (DefaultSqlSession)sqlSession.select() 

可见，最后Mapper都会使用sqlSession去执行SQL。继续看。

(DefaultSqlSession)sqlSession.select()-> 
(BaseExecutor)executor.query() -> queryFromDatabase() -> (SimpleExecutor)executor.doQuery() ->  
(SimpleStatementHandler)handler.query() ->  statement.execute(sql) 

可以阅读SimpleExecutor.doQuery()的代码，可以发现拦截器（Inteceptors）会在这里被调用。  
当见到statement的时候，就是JDBC部分的代码啦。  

### mybatis注解大全

参考：[https://blog.csdn.net/u013452337/article/details/100693418](https://blog.csdn.net/u013452337/article/details/100693418)

|注解|说明|用法|
|---|---|---|
|@Select|绑定select语句到方法 | ```@Select("select * from student") List<Student> query();  ```|
|@Insert|绑定Insert语句到方法 | |
|@Update|绑定Update语句到方法 | |
|@Delete|绑定Delete语句到方法 | |
|@Results|将table的列与object的字段建立映射 |  ```@Results(id="query",value={}) List<Student> query();  ```|
|@Result| | |
|@Param| | |
|@Select| | |
|@Select| | |


### 流式查询

假设有一个需求：需要你查询出某个表的数据，做一些数据格式转换后导出到Excel，且每次导出的数据可能会很多（千万级），你会怎么做？

如果直接查出，千万级的数据要加载到内存，很有可能会导致OOM（内存溢出）；  

分页查询是一种方法，每次查出1w条，写入excel后，再查出下一个1w条，依此类推；  

今天介绍另一种方法：流式查询。  

【什么是流式查询】  
流式查询，是指java进程提交sql给数据库执行，数据库查到结果后，java进程一条一条地从数据库中获得结果并处理，  
而不是直接将所有结果加载到进程的内存空间中。  

【mybatis实现流式查询的原理】  
见DefaultResultSetHandler#handleResultSet方法。  


【mybatis流式查询实战】  
```java
interface StudentMapper {
    //流式查询接口：返回空值，增加一个ResultHandler参数
    void query(@Param("request") Student request, ResultHandler<Student> handler);

}
```

StudentMapper.xml里，要加上fetchSize="-2147483648"
```xml
<select id="query" 
            fetchSize="-2147483648"
            resultMap="bastResultMap">
        select
            *
        from student
    </select>

```

调用StudentMapper
```java

```


