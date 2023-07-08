# Springframework



### 源码学习





#### Spring提供的参数校验类：Assert

Assert，又称为断言，若满足不了Assert中定义的条件，则会抛出异常。

以下是Assert的一些用法

```java
//判断a是否大于10，若小于则抛出异常（IllegalArgumentException）以及入参中的文案。
Assert.isTrue(a>10,"a is smaller than 10!");

Assert.notNull(a,"a cannot be null!");

Assert.isNull(a,"a must be null!");

//还有关于字符串、数组的断言，不一一列举...

```


#### 包扫描的原理：ImportBeanDefinitionRegistrar

使用场景：
* OpenFeign框架，扫描修饰了@FeignClient的接口（@EnableFeignClients、FeignClientsRegistrar）
* Mybatis框架，扫描所有Mapper接口（@MapperScan、MapperScannerRegistrar）
* SpringFramework框架，扫描所有@Async修饰的方法（@Async、AsyncConfigurationSelector）


#### 自动注入（Autowired）的原理

略