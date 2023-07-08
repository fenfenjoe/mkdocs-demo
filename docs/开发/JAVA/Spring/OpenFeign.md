# OpenFeign

**OpenFeign 和 Feign 的区别**

Netflix将Feign贡献给开源组织，就有了OpenFeign；

在Feign的基础上，OpenFeign还有以下优化：

* 支持SpringMVC注解
* 整合了Ribbon和Hystrix
* Feign底层是用RPC协议；而OpenFeign实际上是对Rest调用进行了封装（HTTP协议）


**引入OpenFeign**

```xml
<dependency>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```


**OpenFeign接口被调用前后的底层流程**

> 参考：[https://blog.csdn.net/Josh_scott/article/details/119955855](https://blog.csdn.net/Josh_scott/article/details/119955855)

* 首先，你需要在启动类上添加@EnableFeignClients注解。
* 编写你的OpenFeign接口（@FeignClient注解）。
* 项目启动（SpringApplication.run()）
    * 【@EnableFeignClients】该注解在项目启动时，会将所有被@FeignClient注解修饰的类注册到Spring Bean中。
    * 【-->FeignClientsRegistrar】实际将@FeignClient注解修饰的类注册到Spring Bean中的类。
    * 【-->FeignClientFactoryBean】会将FeignClient接口注册为这个FactoryBean
*  Spring扫描其他Bean，发现有的Bean需要注入你的FeignClient（@Autowired），此时需要生成一个FeignClient实例
    * FeignClientFactoryBean是通过动态代理的方法，来生成一个FeignClient实例的，流程如下：
    * 【FeignClientFactoryBean.getObject()-->loadBalance()-->DefaultTargeter.target()-->Feign.Builder.target()】
    * 【-->ReflectiveFeign.newInstance()】最后是使用ReflectiveFeign，为你生成一个动态代理类：ReflectiveFeign.FeignInvocationHandler
* 项目启动完，当你的FeignClient中的方法被调用时：
    * 【ReflectiveFeign.FeignInvocationHandler.invoke()】最后都会进到这个方法。
    * 【-->SynchronousMethodHandler.executeAndDecode()】重试在这里执行。（即若下面的调用抛异常，会根据配置重新执行）
    * 【-->LoadBalancerFeignClient.execute()】到这个方法后，会根据配置，封装好负载均衡器（Ribbon）
    * 【-->FeignLoadBalancer.executeWithLoadBalancer()】FeignLoadBalancer就是Ribbon的负载均衡器。到这个方法，执行真正的负载均衡，真正的调用，并返回Response
        * 【LoadBalancerCommand】涉及到RxJava框架，略...
