#### Spring

##### Spring Bean的作用域
* singleton：单例模式（默认）
* prototype：原型模式（每次获取Bean都会新建一个实例）
* request：在一次Http请求中，容器会返回该Bean的同一实例
* session：在一次Http Session中，容器会返回该Bean的同一实例
* global Session：在一个全局的Http Session中，容器会返回该Bean的同一个实例

##### ioc和aop说一下
IOC：
重耦合：形容系统、模块或者类之间联系紧密，容易“牵一发而动全身”，某个部分需要改动时会影响到其他部分，是设计不好的一种体现，在Java中就体现在对象与对象之间的关系。
IOC即“控制反转”，通过对象容器去管理对象的注册、初始化以及依赖。对象之间若想建立依赖，只要修改容器的配置即可，而不用去修改源码。

AOP：
面向切面编程，本质上也是为了减轻系统耦合性的一种架构。
有许多业务之外的操作，是在调用一些函数的前后经常需要用到的，比如事务的开启与关闭、写日志等等。
若每次调用都要手动去写这些内容，一会增加工作量，二是当这些操作需要换一种方式去实现的时候，修改的工作量会很大。
AOP通过代理模式，提供了一种“代码织入”的方法。


##### springboot运行原理
1. 自动配置类加载：初始化classpath下 META-INF/spring.factories中已配置的ApplicationContextInitializer、ApplicationListener
2. 配置(yml等)读取类加载：初始化classpath下 META-INF/spring.factories中已配置的 SpringApplicationRunListeners
3. 构造应用上下文环境：Java环境、Spring运行环境、Spring项目运行环境
4. 初始化应用上下文
5. 刷新容器（Ioc容器在这进行初始化）


##### springmvc执行流程



##### Spring事务
###### 如何配置Spring事务
编程式事务（TransactionTemplate）
声明式事务（XML、注解注入）

###### Spring事务有哪些隔离级别？有哪些传播机制（回滚机制）？

隔离级别：见枚举类Isolation。默认是Isolation.DEFAULT，即按照数据库的隔离机制。  

回滚机制：见枚举类Propagation。默认是Propagation.REQUIRED，即若当前有事务，则按当前事务运行；没有事务则新建事务。  



##### Spring是如何解决循环依赖问题的？



##### SpringBoot的类加载问题

* 启动类是什么时候加载的？

* spring.factories里的类是什么时候加载的？

* @Bean、@Import指明的类是什么时候加载的？

* @Component、@Service、@Controller等是什么时候加载的？


##### SpringMVC的Controller返回一个java对象，传给前端的时候会自动变成json对象，这是怎么做到的？

