# SpringBoot



### Spring Boot的启动流程原理

#### 代码版流程（简易）
* SpringApplication
    * Listener -> start 
    * Environment -> prepare -> config 
    * Context -> create -> prepare -> postProcess -> load -> refresh
    * BeanFactory -> obtain -> prepare -> postProcess -> invokePostProcessors

#### 代码版流程
1. new SpringApplication()  
    1. deduceWebApplicationType()  
		> 初始化、判断该应用的web类型：Servlet、Rective、None
		> * SERVLET: 应用程序应作为基于servlet的web应用程序运行，并应启动嵌入式servlet web（tomcat）服务器。
		> * REACTIVE: 应用程序应作为 reactive web应用程序运行，并应启动嵌入式 reactive web服务器。  
	2. setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));  
		> 初始化classpath下 META-INF/spring.factories中已配置的ApplicationContextInitializer  
        > 若你的项目中没配置，那就是加载spring-boot.jar里面的META-INF/spring.factories  
        > 这里“初始化”的意思是，读取文件中的信息，并保存到某个字段中。下同。  
        > 像这里就是将spring.factories中的配置解析、并加载到SpringApplication.initializers字段中。    
	3. setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));  
		> 初始化classpath下 META-INF/spring.factories中已配置的 ApplicationListener  
        > 若你的项目中没配置，那就是加载spring-boot.jar里面的META-INF/spring.factories    

2. SpringApplication.run()  
	1. SpringApplicationRunListeners listeners = getRunListeners(args);  
		> 获取监听器（spring.factories中配置的SpringApplicationRunListeners）  
		> 监听器：  
		>	* ConfigFileApplicationListener：负责从默认路径加载项目配置文件
        >        * classpath: 
        >        * file:./
        >        * classpath:config/
        >        * file:./config/
		>	* LoggingApplicationListener
		>	* ...  
	2. listeners.starting();  
		> 启动监听器，下面开始构建应用上下文
	3. ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
		> 将main函数的入参封装一下
	4. ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
		> 构造应用上下文环境，包括：计算机的环境，Java环境，Spring的运行环境，Spring项目的配置
		1. configureEnvironment()
			> 创建并配置environment系统环境，会将main参数加入到Environment
			1. configurePropertySources()
				> 将main 函数的args加入环境中
			2. configureProfiles()
				> 【重点】激活相应的配置文件（根据spring.profiles.active参数）
		2. listeners.environmentPrepared（环境准备完毕，通知各监听器）
			> 【重点】此时ConfigFileApplicationListener会从默认路径加载项目配置文件（读取yml、properties）
	5. ConfigurableApplicationContext context = createApplicationContext();
		> 初始化应用上下文  
		> 会根据不同的应用类型，创建不同的应用上下文。其中Servlet对应的是AnnotationConfigServletWebServerApplicationContext  
		> 初始化IOC容器（BeanFactory）。应用上下文对IOC容器是持有关系，其默认实现是DefaultListableBeanFactory。  
	6. prepareContext(context, environment, listeners, applicationArguments, printedBanner);
		> 刷新应用上下文前的准备阶段  
        > 工作包括：  
        > 将环境变量放进context里、执行Initializer、加载启动类的BeanDefinition到context里（其实是BeanFactory里）
		1. postProcessApplicationContext(context);
			> 执行容器后置处理  
            > 实际上就是为context添加两个组件：  
            > * beanNameGenerator
            > * resourceLoader
		2. applyInitializers(context);
			> 执行所有Initializer
		3. listeners.contextPrepared(context);
			> 容器准备完毕，通知各监听器
		4. load(context, sources.toArray(new Object[0]));
			> 将我们的启动类，以BeanDefinition的形式注册进BeanFactory  
		5. listeners.contextLoaded(context);
			> 上下文装载完毕，通知各监听器
	7. refreshContext(context);
		> 上一步已准备好IOC容器（BeanFactory），下面进行应用上下文（context）的刷新
		1. applicationContext.refresh
			1. prepareRefresh();
				> 准备刷新
			2. beanFactory = obtainFreshBeanFactory();
				> 拿到之前创建好的beanFactory
			3. prepareBeanFactory(beanFactory);
				> 为beanFactory：
				> 	* 配置类加载器（setBeanClassLoader(ClassUtils.getDefaultClassLoader())）
				> 	* 配置EL表达式Resolver（setBeanExpressionResolver(StandardBeanExpressionResolver)）
				> 	* 添加第一个Bean的后置处理器（addBeanPostProcessor(ApplicationContextAwareProcessor)）
                >   * ...
			4. postProcessBeanFactory(beanFactory);
				> 可以在此向上下文中添加一些的Bean的后置处理器  (BeanPostProcessor)  
				> 后置处理器工作的时机是在所有的beanDenifition加载完成之后，bean实例化之前执行  
			5. 【重点】invokeBeanFactoryPostProcessors(beanFactory,beanFactoryPostProcessors);
				1. 实例化并调用所有BeanFactory后置处理器（BeanFactoryPostProcessor）
					> 有两类BeanFactoryPostProcessor:BeanFactoryPostProcessor和BeanDefinitionRegistryPostProcessor
                    1. 执行入参中的所有BeanDefinitionRegistryPostProcessor.postProcessBeanDefinitionRegistry()
                    2. 执行BeanFactory中的所有BeanDefinitionRegistryPostProcessor.postProcessBeanDefinitionRegistry()
                        1. 先执行实现PriorityOrdered接口的
                        2. 再执行实现了Ordered接口的
                        3. 最后执行剩下的
                    3. 执行BeanFactory中的所有BeanDefinitionRegistryPostProcessor.postProcessBeanFactory()
                    4. 执行入参中的所有BeanDefinitionRegistryPostProcessor.postProcessBeanFactory()
                    5. 执行BeanFactory中的所有BeanFactoryPostProcessor.postProcessBeanFactory()
                        1. 先执行实现PriorityOrdered接口的
                        2. 再执行实现了Ordered接口的
                        3. 最后执行剩下的 
                3. IoC容器的初始化过程完成
			6. registerBeanPostProcessors(beanFactory);
                > 把实现了BeanPostProcessor接口的类实例化，并且加入到BeanFactory中
			7. initMessageSource();
                > 国际化
			8. initApplicationEventMulticaster();
                > 初始化事件管理类
            9. onRefresh();
                > 这个方法着重理解模板设计模式，因为在springboot中，这个方法是用来做内嵌tomcat启动的
			10. registerListeners();
                > 往事件管理类中注册事件类
			11. 【重点】finishBeanFactoryInitialization(beanFactory);
                > 进行一次Bean实例化（只限于scope=non-lazy 的Bean，多实例Bean在第一次getBean()才会被实例化）
                > Bean的scope默认都是"non-lazy"
			12. finishRefresh();
	8. afterRefresh(context, applicationArguments);
		> 刷新应用上下文后的扩展接口
	9. listeners.started(context);
		> 发布容器启动完成事件
	10. listeners.running(context);
	


### Spring Boot 常见概念

* SpringApplication
* ApplicationListener
* ApplicationContextInitializer
    > 一些初始化操作，在环境（Environment）初始化好之后、BeanDefinition加载之前执行。
* Banner
    > 横幅的意思。SpringBoot启动时，控制台会显示出一个用字符组成的“logo”，这个logo就叫banner。
* BeanDefinition
    > “生产Bean的说明书”，由BeanFactory对象保存并维护。
* BeanFactoryPostProcessor
    > 该接口用来在BeanFactory初始化好后，可以添加自己的一些逻辑。
    > 有两种添加BeanFactory后置处理器的方法：
    > 1. ConfigurableApplicationContext#addBeanFactoryPostProcessor()  ---【硬编码注入】这种一般是SpringBoot自己添加的用法
    > 2. 创建一个BeanFactoryPostProcessor接口的实现类   ---【配置注入】只要被扫描进BeanFactory，也会被执行
    * 实现类：
        * 
* BeanDefinitionRegistryPostProcessor
    > 该接口继承了BeanFactoryPostProcessor，比BeanFactoryPostProcessor更优先执行
    * 实现类：
        * ConfigurationClassPostProcessor
            1. 调用了ConfigurationClassBeanDefinitionReader来做BeanDefinition初始化（分三步）：
                1. Resource定位：
                    > 解析主类BeanDefinition，获取主类所在的basePackage（路径）  
                    > 通过SPI扩展机制实现的自动装配（各种starter），获取basePackage的路径  
                    > 加载@Import注解，获取basePackage的路径  
                    > 最后basePackage的格式：classpath*:org/springframework/boot/demo/**/*.class  
                2. BeanDefinition的载入
                    > 将basePackage路径下的所有class都load进来JVM
                3. 注册BeanDefinition
                    > 将所有load进来的class，以BeanDefinition的形式，注册到beanFactory的beanDefinitionMap中
                >* 加载工具：
                >   * BeanDefinitionLoader 
                >	* annotatedReader （注解形式的Bean定义读取器）
                >	* xmlReader （XML形式的Bean定义读取器）
                >	* scanner （类路径扫描器）
                >* 加载方式：
                >    * 从Class加载（我们的主类会按Class加载）
                >    * 从Resource加载
                >    * 从Package加载
                >    * 从 CharSequence 加载
            2. ConfigurationClassParser.parse()
                1. parse()
                >    载入主类所在basePackage的BeanDefinition  
                >    对主类路径下的Bean递归载入（包括Bean内定义的子Bean）：  
                >        ... processPropertySource()  
                >            针对 @PropertySource 注解的属性配置处理  
                >        ... componentScanParser.parse()  
                >            根据 @ComponentScan 注解，扫描项目中的Bean，并递归查找该类相关联的配置类  
                >        ... processImports  
                >            递归处理 @Import 注解，加载Import指定的类  
                >            各种@EnableXXX注解，很大一部分都是对@Import的二次封装  
                2. processDeferredImportSelectors()
                >    springboot自动装载的入口  
                >    找到@SpringBootApplication import进来的  
                >        AutoConfigurationImportSelector  
                >            getCandidateConfigurations(annotationMetadata, attributes);  
                >                读取spring-boot-autoconfigure工程下的meta-inf/spring.factories，获取自动配置类  
                >            checkExcludedClasses(configurations, exclusions);  
                >                需要排除的自动装配类（@SpringBootApplication(exclude ={ ...} )）  
                >            filter(configurations, autoConfigurationMetadata);  
                >                根据@CondiionOnXXX规则，过滤掉不需要装配的类  
                >            获得一些自动配置类  
                >        最后将这些类解析成BeanDefinition并注册进beanDefinition中 
        * MapperScannerConfigurer
            > Mybatis：扫描basePackage中的Mapper Bean

* BeanPostProcessor
    > 该接口用来在Bean实例化时、实例化后，添加自己的一些逻辑。
    * ApplicationContextAwareProcessor
        > 为Bean注入ApplicationContext
    * AutowiredAnnotationBeanPostProcessor  
        > 为Bean处理@Autowired注解
    * CommonAnnotationBeanPostProcessor
        > 为Bean处理@Resource注解



### Spring Boot 组件结构

* ApplicationContext
    * parentContext(也是一个ApplicationContext)
    * BeanFactory
    * Environment
    * List<BeanFactoryPostProcessor>
    * List<ApplicationListener>

### 三种注入Bean实例的方法

1. @Import(Bean.class)

2. @Import + 实现ImportSelector接口  
（实际使用例子：见@SpringBootApplication -> @EnableAutoConfiguration -> AutoConfigurationImportSelector）

    ```java
        //1.实现ImportSelector接口
        public class MyImportSelector implements ImportSelector {
            //2. 重写selectImports()，返回需要实例化的Bean的全类名
            @Override
            public String[] selectImports(AnnotationMetadata annotationMetadata) {
                Set<String> annotationTypes = annotationMetadata.getAnnotationTypes();
                for (String str:annotationTypes) {
                    System.out.println(str);
                }
                return new String[]{"com.picc.spring.annotation.Dog"};
            }
        }
        //3.将实现类import
        @Import({MyImportSelector.class})
        @Configuration
        public class AppConfig {
        }
    ```
3. @import + 实现ImportBeanDefinitionRegistrar类  
略




### Spring Boot Starter

**有什么作用？**

* 加载默认配置（AutoConfig）

> 启动时，需要加载哪些默认配置类，全部记录在springboot-autoconfigure的META-INF/spring.factories里