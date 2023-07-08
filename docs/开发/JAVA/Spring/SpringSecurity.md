# SpringSecurity


#### Spring Security用例
##### spring-boot-security-starter
主要作用：
1. 依赖了spring-security-config和spring-security-web两个包
2. 启动时加载自动配置类：SecurityAutoConfiguration

##### 依赖Spring Security
1. 添加依赖
```xml
<dependency>    
    <groupId>org.springframework.boot</groupId>    
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>        
    <groupId>org.springframework.boot</groupId>        
    <artifactId>spring-boot-starter-web</artifactId> 
</dependency>
```

2. 启动App，在浏览器访问localhost:8080，发现弹出登录界面（需要用户名密码）

3. 初始账号为user，密码在控制台的日志中

> 这些都是Spring Security的默认配置，假如想输入用户密码后去数据库查用户信息，并返回权限，该怎么做呢？


##### 自定义登录逻辑：UserDetailService接口
通过重写一个UserDetailService接口的实现类，可以自定义登录的逻辑。

UserDetailService接口中有一个UserDetail loadUserByUsername(String name)的方法。

将“去数据库查询用户信息”的逻辑重写至该方法，那么点击登录以后，便会走到loadUserByUsername的这段逻辑。（可打断点尝试）

> 并不需要Controller，只要访问localhost:8080，便会自动走到这个UserDetailService接口的方法中。
> 现在，有了自己的自定义登录逻辑，想用自定义的登录页面，该怎么办？

##### 自定义登录页面、登录url、登录成功页面
新建以下的配置类
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override    
    protected void configure(HttpSecurity http) throws Exception {        

        http.formLogin().loginPage("/login.html") //自定义登录界面 
          .loginProcessingUrl("/login") //HTML中表单登录按钮对应的url地址 
          .successForwardUrl("/successfulLogin");//登录成功后跳转的页面

     }
 }
```

>想为用户添加一些权限控制，想控制一些接口（url）需要用户登录才能访问，该怎么办？

##### 访问控制（配置类）
访问控制有两种方式可以配置：1.配置类 2.注解

**配置类**

同样是上面的配置类中添加。
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override    
    protected void configure(HttpSecurity http) throws Exception {        

        http.formLogin().loginPage("/login.html") //自定义登录界面 
          .loginProcessingUrl("/login") //HTML中表单登录按钮对应的url地址 
          .successForwardUrl("/successfulLogin");//登录成功后跳转的页面

         //以下代码便是配置权限控制的地方。
         
         //配置页面的认证规则 
         http.authorizeRequests() 
         // 跨域预检请求无需认证
         .antMatchers(HttpMethod.OPTIONS, "/**").permitAll() 
         // 登录 无需认证
         .antMatchers("/login").permitAll() 
         // 注册 无需认证
         .antMatchers("/register").permitAll()
         //main1 有main1权限才能直接访问，否则走认证（登录）流程
         .antMatchers("/main1.html").hasAuthority("main1")
         //main1 有main1或者main2权限才能直接访问，否则走认证（登录）流程
         .antMatchers("/main1.html").hasAnyAuthority("main1","main2")
         //main2 有admin角色才能直接访问，否则走认证（登录）流程
         .antMatchers("/main2.html").hasRole("admin")
         //access表达式：结果同上，只是写法不一样
         .antMatchers("/main2.html").access("hasRole('admin')")
         //还可以通过access()调用自定义的权限判定方法
         //里面是access表达式
         //意思是：通过MyServiceImpl里面的hasPermission方法判定
         .antMatchers("/main2.html")
         .access("@myServiceImpl.hasPermission(request,authentication)")
         // swagger 
         .antMatchers("/swagger**/**").permitAll() 
         .antMatchers("/webjars/**").permitAll()
         .antMatchers("/v2/**").permitAll()
         // 其他所有请求需要身份认证
         .anyRequest().authenticated();
     }
 }
```
> 问：在哪里配置用户的角色和权限？
> 答：在上面提到的UserDetailService接口的实现类中。当查出UserDetail（用户信息）时，需要把它的List<GrantedAuthority>（也就是权限列表）也查出来。
> 以下为示例代码：
> ```java
> public UserDetails loadUserByUsername(String username) throws 
> UsernameNotFoundException {
>    //...
>    List<GrantedAuthority> grantedList =AuthorityUtils.commaSeparatedStringToAuthorityList(
>    "ROLE_admin","ROLE_normal", //角色，必须要“ROLE_”开头
>    "main1","main2" //权限，没格式限制
>    )
>    return new User(
>    username,  //用户名
>    password,  //密码
>    grantedList //角色和权限
>    );
>  }
> ```

###### 获取登录用户的信息
```java
//登录后，authentication=登录当事人或令牌（token）；若未登录则返回null
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
//登录人信息，一般为SpringSecurity提供的UserDetail的实现类。里面保存登录人的信息
Object obj = authentication.getPrincipal();
String username;
if(obj instanceof UserDetails){
    username = ((UserDetails)obj).getUsername();
}
```

###### 访问控制匹配url的规则
可以通过ant表达式来匹配url。
```
?：匹配一个字符
*：匹配0个或多个字符
**：匹配0个或多个目录
```
示例：
```java
http.authorizeRequests()
  .anyMatcher("/image/**").permitAll //image下所有文件不用验证，可直接访问
  .anyMatcher("/login").permitAll //login不用验证
  .anyMatcher("/**/*.html").permitAll; //所有html页面不用验证
```

可以通过正则表达式来匹配url。

示例：
```java
```

**注解**

若需要使用注解配置接口的访问权限，需要现在配置类上加上以下注解：
```java
@EnableGlobalMethodSecurity(
securedEnable=true, //想通过角色判断，设这个为true
prePoseEnable=true,//想通过角色或者权限判断，设这个为true
)
```

1.判断是否具有角色：
```java
//必须要加上"ROLE_"作为前缀，若无权限，则需要先登录；登录后还无权限则返回500
@Secured("ROLE_admin")
public void myMethod(){
...
}
```

2.判断是否具有权限：
```java
//执行前判断是否有权限，若无权限，则需要先登录；登录后还无权限则返回500
//里面是access表达式
@PreAuthorize("hasRole('admin')")
public void myMethod(){
...
}
//执行后判断是否有权限，若无权限，则需要先登录；登录后还无权限则返回500
//里面是access表达式
@PoseAuthorize("hasRole('admin')")
public void myMethod(){
...
}
```



>用户若没有权限访问接口，通常服务器会返回403的报错。这样直接返回报错不太友好，该怎么办？

##### 自定义403方案
同样在配置类中进行配置。
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override    
    protected void configure(HttpSecurity http) throws Exception { 
      //自定义一个403处理器
      http.exceptionHandling().accessDeniedHandler( new MyDeniedHandler());
    }
}    
```

>每次都要登录太麻烦，如何设置一个类似于“自动登录”的功能？

##### REMEMBER ME 功能
在登录界面添加一个单选框“记住我”，name为“remember-me”；

在配置类中添加remember me的配置。
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    UserDetailService userDetailService;
    @Autowired
   TokenRepository tokenRepository;
    @Override    
    protected void configure(HttpSecurity http) throws Exception {        
        //这两个类都要自行实现。
        //userDetailService：获取用户信息的接口
        //tokenRepository：保存登录信息的接口
        http.rememberMe()
          .userDetailService(userDetailService)
          .tokenRepository(tokenRepository)
     }
 }
```
> 登录后的事情解决了，那么登出的接口又该如何写？

##### 跨域配置（CORS）

1.全局配置：同样在配置类中进行配置。
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    public CorsFilter corsFilter(){

        // 初始化cors配置对象
        CorsConfiguration configuration = new CorsConfiguration();

        // 设置允许跨域的域名,如果允许携带cookie的话,路径就不能写*号, *表示所有的域名都可以跨域访问
        configuration.addAllowedOrigin("http://127.0.0.1:5500");
        // 设置跨域访问可以携带cookie
        configuration.setAllowCredentials(true);
        // 允许所有的请求方法 ==> GET POST PUT Delete
        configuration.addAllowedMethod("*");
        // 允许携带任何头信息
        configuration.addAllowedHeader("*");

        // 初始化cors配置源对象
        UrlBasedCorsConfigurationSource configurationSource = new UrlBasedCorsConfigurationSource();

        // 给配置源对象设置过滤的参数
        // 参数一: 过滤的路径 == > 所有的路径都要求校验是否跨域
        // 参数二: 配置类
        configurationSource.registerCorsConfiguration("/**", configuration);

        // 返回配置好的过滤器
        return new CorsFilter(configurationSource);
    }
}    
```

2.局部允许跨域：@CrossOrigin
```java
//方法1：在类上注解，则所有方法均允许跨域
@Controller
@RequestMapping("/test8")
@CrossOrigin(origins = "http://127.0.0.1:5500") 
public class Test8Controller {

}



//方法2
@Controller
@RequestMapping("/test8")
public class Test8Controller {

    //在方法上注解
    @RequestMapping("/test")
    @CrossOrigin(origins = "http://127.0.0.1:5500")
    public String test(){
        //...
    }
}
```


##### 退出登录
Spring Security支持默认的登出功能，只要在浏览器调用/logout请求即可。
调用后会自动跳回到登录页面。

如果需要自定义登出功能，则需要修改配置类。原理跟自定义登录功能差不多。
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override    
    protected void configure(HttpSecurity http) throws Exception {        

        http.logout()http.logout().logoutUrl(); //TML中表单登录按钮对应的url地址

     }
 }
```
##### 小结
上面提供了通过spring boot整合spring security的一些小demo，授权模式用的是security框架默认的用户名密码模式。当我们需要用到第三方认证时（如微信、QQ、微博等），则需要通过另一个框架：`Spring Security Oauth2`

#### Spring Security Oauth2
Oauth2下的授权服务器一般会提供以下4个端点（接口）：
* `Authorize EndPoint（/oauth2/authorize）`：授权端点，进行授权（返回授权码）
* `Token EndPoint（/oauth2/token）`：令牌端点，获取Token
* `Introspection EndPoint（/oauth2/introspect）`：校验端点，校验Token的合法性
* `Revokation EndPoint（/oauth2/revoke）`：撤销端点，撤销令牌









#### Spring Security原理
##### 过滤器
Spring Security本质上是一个过滤器链。底层应用了Servlet的Filter过滤器链。

每次HTTP请求进来，都会先经过过滤器链对请求进行预处理。

> **FAQ**
>* 请求进来时，每个过滤器方法都会调用吗？
>是的。
>
>* 可以简单介绍一下Servlet的过滤器链的原理吗？
>   1. 过滤器的生命周期：tomcat将HTTP request封装成request  ---> 过滤器链 ----> servlet生成response ---> 过滤器链 ----> tomcat返回HTTP response；
>   2. 过滤器的执行顺序与web.xml中的<filter-mapping>标签相关（谁在前先执行谁）
 >* 


Spring Security默认的过滤器链：
![24f17ce39b7f04f2f60d49415307f658.png](en-resource://database/1109:1)

**SecurityContextPersistenceFilter**：获取SecurityContext

**BasicAuthenticationFilter**：

**UsernamePasswordAuthenticationFilter**：获取用户名和密码，封装后交由AuthenticationManager进行验证；
> 【验证原理】
UsernamePasswordAuthenticationFilter -> 
AuthenticationManager -> 
AuthenticationProvider
实际上是通过一个管理器（Manager）去管理多种校验方式（Provider）
【AuthenticationManager原理】
维护了多种校验方式（AuthenticationProvider的List），只要有其中的一个AuthenticationProvider认证成功（即返回一个非空的Authentication），则代表认证成功，不会继续走其他的AuthenticationProvider；
AuthenticationProvider默认实现类为DaoAuthencationProvider，即从数据库获取用户信息。
【DaoAuthenticationProvider认证过程】

**ExceptionTranslationFilter**：处理AuthenticationException 和 AccessDeniedException两类异常

**FilterSecurityInterceptor**：校验是否有权限调用该方法（uri）

###### 过滤器如何加载

FilterComparator可查看过滤器加载顺序。


**无Spring Boot加载**
DelegatingFilterProxy 加载 FilterChainProxy
FilterChainProxy 加载 List<Filter> （过滤器链）

**Spring Boot自动加载**
@EnableWebSecurity
>加载了四个类：
>WebSecurityConfiguration
>SpringWebMvcImportSelector
>OAuth2ImportSelector
>AuthenticationConfiguration