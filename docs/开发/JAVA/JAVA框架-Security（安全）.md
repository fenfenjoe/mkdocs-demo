# JAVA框架-Security（安全）
[toc]
## 参考
【阮一峰的网络日志-OAuth 2.0 的四种方式】[https://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html](https://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html)  
【Spring Security+ JWT项目实战视频】[https://www.bilibili.com/video/BV1vK4y1H7b1](https://www.bilibili.com/video/BV1vK4y1H7b1)  

## 认证、鉴权的使用场景

【用户登录】
现在用户登录，一般采用以下几种方式：
* 用户名/密码
* 微信/支付宝等第三方授权
* 单点登录
我们会在后面分析一下这几种方式的原理是怎样的。

【接口调用】
一般分为两步：  
1. 获取token（一般使用客户端模式，入参一般包括id、secret、timestamp、sign）
  * 发起请求，入参一般包括id、secret、timestamp、sign
  * id、secret由接口提供方提供；timestamp就是调用时的时间戳；
  * sign一般是由id、secret、timestamp通过加密算法生成的一个字符串；
  * 接口提供方收到请求后，会用同样的加密算法对报文再计算一遍sign；若相同则返回token
2. 每次接口调用时都带上token




### 用户名/密码（HTTP BASIC）
1. 用户在表单填写用户名/密码，并提交至服务器。传参有几种方式：  
  1.1 放在表单中（即HTTP Body中）  
  1.2 放在Authentication头中（Authentication:BASIC [用户名+密码的BASE64编码]  
2. 服务器验证成功后，返回SESSION ID。  
    HTTP响应报文头增加"set-cookie:[SESSION ID]"，这样用户下次发起请求时，便自动将SESSION ID通过cookie传给服务器。  
3. 前端获取到session id代表登录成功
4. 下次请求时，会自动将cookie传给服务端

### 第三方授权登录（OAuth）

考虑以下场景：用户想使用APP A，但又不想在A上注册账号；此时，允许用户使用APP B的账户登录APP A的授权过程，就是“第三方授权登录”。

授权，实质就是APP A引导用户去APP B获取到用户在APP B上面的token（令牌），然后APP A就可以根据token访问用户在APP B上的用户信息了。
（ID、头像、昵称等等）。

下面介绍最常见的一种授权方式。其他方式见[这里](#6获取token的方式)

1. 前端请求服务器资源时，服务器发现没有登录信息，重定向到登录页面
2. 在登录页面选择第三方（比如微信）登录，此时又跳转到微信授权页面
3. 用户选择授权，微信将授权码返回给服务器。
4. 服务器拿着授权码，再去微信服务器取得token。token有两种类型：  
  4.1 Authentication:Bearer sF_6.B7f-8.9xmP（JWT也是用这种方式）  
  4.2 Authorization: MAC id="t67vi01hd3",nonce="849662:fs873ka",mac="NsdHsdndKBkjfKfhKJN="  
5. 下次请求时，可以通过以下方式将token传给服务端  
  5.1 放在URI  
    GET /index.html?access_token=sF_6.B7f-8.9xmP  
  5.2 放在请求头
    Authorization: Bearer sF_6.B7f-8.9xmP
  5.3 放在请求体
    access_token=Bearer sF_6.B7f-8.9xmP


### 单点登录

当你的大型应用被重构成多个子应用时，那么可能就会有单点登录这个需求。  
单点登录需求简单来说，是指“只要你在A应用登录过一次，在你访问B、C应用时也可以无需重新登录，可以直接访问”。  
单点登录功能一般由IAM系统实现。其他系统通过调用IAM系统的API来实现注册、登录、注销功能，同时通过IAM系统获取用户信息。  
IDaas（IAM + SAAS），意思就是云供应商（阿里云、腾讯云等）将IAM这些需求变成一个云服务，开发者无需重复开发自己的IAM系统，直接使用这个云服务即可。

## 概念


#### 为什么需要cookie、session、token这些东西
HTTP协议是无状态，无法标识哪条请求来自哪个用户。

因此，需要为每个用户分配一个随机字符串作为ID，去标识用户，从而区分请求来自哪个用户。session面世。

session id 便是服务端为每个用户分配的唯一标识。

后来发现，服务端管理session不方便（要解决如何在服务器集群存储session的问题），因此，想将session id放到客户端存储。此时cookie面世。

通过cookie，将sessionid存储在前端（浏览器），每次请求时,session id自动通过cookie传给服务器。  

再后来，移动端应用兴起，这些应用无法使用cookie，或者有的浏览器有禁用cookie的功能；cookie无法覆盖这些登录场景，此时token面世。

服务端将token放到http body中传给前端，不再用cookie；而前端则可以将token存到LocalStorage，以代替cookie。
  
  
  
## cookie



##### 1.什么是cookie
* cookie cookie有点像浏览器维护的一个数据结构为Map的对象，存储着很多key-cookieObject
* cookie 是不可跨域的
###### cookie的缺点
* `cookie`数量和长度有限制
* 用户可将cookie配置为禁用状态
* cookie可以被用户篡改、拦截，不安全
* 每次cookie都会跟随请求发送到服务端
###### cookieObject(一个cookie的格式)
```properties
#cookie名称，必填项
name=value;  
#可访问该cookie的域名
domain=www.domain.com; 
#可访问该cookie的路径
path=/test/index.html; 
#过期时间
expires=Sat, 11 Jun 2016 11:29:42 GMT; 
#有效期，秒数，优先级高于expires，负数表示退出浏览器会删掉
max-age=-1; 
#JS不能读取该cookie
HttpOnly; 
#是否只有HTTPS才能读取该cookies
secure=false;
```
##### 2.如何在浏览器查看cookie
打开浏览器-按F12或右键点击“检查”，进入开发者界面-单击某一条HTTP请求，可在HTTP请求中查看得到
##### 3.如何使用cookie
服务端->客户端：服务器在响应报文中添加一个报文头。（set-cookie： cookie内容）

客户端->服务端：客户端在请求报文中添加一个报文头。（cookie：cookie内容）
##### 4.cookie如何保存
客户端：保存在硬盘中。
服务端：不保存cookie。

## session
##### 1.什么是session
每个用户保存在服务器的一个唯一标识（字符串）。
###### session的缺点
* 容易遭受CSRF攻击（跨站伪造请求）
* 服务端需要保存并维护所有用户的session id，增加了负担
##### 2.如何生成session
由服务端生成。

生成时间：在tomcat中，调用HttpServletRequest.getSession(true)时创建。 
生成规则：tomcat的ManagerBase类提供创建sessionid的方法（随机数+时间+jvmid）；  

##### 3.如何使用session
略

##### 4.session如何保存
服务端：保存在内存中，也可以将其持久化。  
客户端：一般保存在cookie中。  



## oauth2.0、token
##### 1.什么是token
又称为“令牌”。当一个客户端要访问某一个服务器时，需要先去他的认证服务器获得访问权限。这个访问权限就叫token。  
token有过期时间、有权限范围。  

```bash
#token请求实例（授权码模式）
https://b.com/oauth/authorize?response_type=code&client_id=CLIENT_ID& 
redirect_uri=CALLBACK_URL&scope=read&client_secret=CLIENT_SECRET 
				
                
#response_type： code=通过授权码模式请求 ；
#client_id：客户端ID
#redirect_uri：重定向URI
#scope：请求授权范围
#client_secret：客户端密钥
 
 #其中：client_id、client_secret都需要提前向认证服务器申请
```

```bash
#token返回实例（在HTTP消息体中传输）
 HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
"access_token":"2YotnFZFEjr1zCsicMWpAA", 
"token_type":"example", 
"expires_in":3600, 
"refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA", 
"example_parameter":"example_value"
}

#access_token：访问令牌，即token值
#token_type
#expires_in：失效时间，单位为秒
#refresh_token：更新令牌，用来获取下一次的访问令牌
```
##### 2.token存储在哪里
浏览器：可以将token保存到LocalStorage或者Cookie中。
服务端：有多种存储方式：1.内存 2. 关系型数据库 3.Redis 4.JwtStore  
##### 3.验证成功后的请求如何携带token，发送给服务器
放到“Authorization”这个http头里。  
一般token也会有个token头，是固定的字符串：“Bearer ”。  
因此发送的格式如下：  
"Authorization":“Bearer+空格+token字符串”  

>通过Poseman发送HTTP请求时，可见有一栏“Authorization”，也可以在此处选择`认证机制`以及填写token。

##### 4.token常见术语
* `客户凭证(client credential)`：用户在客户端的用户名以及密码
* `令牌(token)`：授权服务器在接收到用户请求后，颁发的“允许访问证明”
* `作用域(scope)`：表明某个令牌指定的细分权限，比如是否可以访问头像、年龄、昵称等
##### 5.token类型
* `访问令牌`
* `刷新令牌`
* `Baraer Token`
* `Proof Of Possession（POP）Token`
##### 6.获取token的方式
###### 授权码模式（authorization code）
比较常用（QQ、微信、GITHUB），适用于第三方应用有后端的情况。
OAUTH2走的就是这个模式。
grand_type=authorization_code，即使用授权码模式。

流程图（**第三方应用** 去 **认证服务器** 取访问 **应用服务器** 的token）
|用户|第三方Web应用|认证服务器|应用服务器|
|---|---|---|---|
|用户请求登录|-->||
||发送请求and重定向URI|-->|
|<---|---|返回登录认证页面|
|用户同意|---|-->|
||<---|返回授权码到重定向URI|
||用授权码请求token|-->|
||<---|认证通过，取得token|
|通过token访问资源|---|--|-->|
|<---|---|--|返回资源|
        
        
详细流程
1. 第三方Web应用向**认证服务器**请求授权码
```bash
# b.com为认证服务器的地址
# https://a.com/callback为第三方Web应用提供自己的重定向地址
https://b.com/oauth/authorize?
  response_type=code& 
  client_id=CLIENT_ID& 
  redirect_uri=https://a.com/callback& 
  scope=read 
```

2. 认证服务器返回授权码 并重定向
```bash
# 可以看到，授权码（code）被放在重定向地址的后面
https://a.com/callback?code=AUTHORIZATION_CODE
#code里面的就是授权码
#认证服务器生成授权码后，根据第三方应用提供的回填地址，将授权码返回给第三方应用
#一般是重定向到第三方Web应用的后端接口，再由后端拿着授权码去获取token
```
3. 第三方Web应用的后端通过授权码向认证服务器 请求Token
```bash
https://b.com/oauth/token?
 client_id=CLIENT_ID&
 client_secret=CLIENT_SECRET&
 grant_type=authorization_code&
 code=AUTHORIZATION_CODE&
 redirect_uri=CALLBACK_URL
```
						
4.认证服务器返回token
```bash
{    
  "access_token":"ACCESS_TOKEN",
  "token_type":"bearer",
  "expires_in":2592000,
  "refresh_token":"REFRESH_TOKEN",
  "scope":"read",
  "uid":100101,
  "info":{...}
}
```

###### 隐藏式授权码模式（implicit）

与授权码模式相比，少了“获取授权码”的步骤，直接从认证服务器获得了token。

适用于第三方应用只有前端的情况。

response_type=token，即直接请求token。

流程图（**第三方应用** 去 **认证服务器** 取访问 **应用服务器** 的token）

|第三方Web应用||认证服务器的授权页面|认证服务器|应用服务器|
|---|---|---|---|---|
|--|(发送请求and重定向URI)|--|-->|
|<---|(重定向并返回token)|--|--|
|----|(通过token访问资源)|--|--|-->|
|<---|(返回资源)|--|--|--|

详细流程
1. 第三方Web应用向认证服务器 请求token
```bash
https://b.com/oauth/authorize?
  response_type=token& 
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
```

2. 认证服务器返回token 并重定向
```bash
https://a.com/callback#token=ACCESS_TOKEN 
#重定向到Web应用，并通过锚点（#）直接传令牌到Web的前端 
#用锚点是为了减少泄漏令牌的风险
```

###### 密码模式（resource owner password credentials）
grant_type=password，即使用密码模式。

流程图
|第三方Web应用||认证服务器|应用服务器|
|---|---|---|---|
|--|用户的账户密码|-->|
|<---|认证通过，返回token|--|
|----|通过token访问资源|--|--|-->|
|<---|返回资源|--|--|--|
				
详细流程
1. 第三方Web应用 通过账户密码，向认证服务器请求token
```bash
https://oauth.b.com/token?
  grant_type=password& //通过密码模式
  username=USERNAME&
  password=PASSWORD&
  client_id=CLIENT_ID //web应用的id
```

2. 不需要跳转，认证服务器把令牌放在 JSON 数据里面，作为 HTTP 回应，第三方Web应用直接拿到令牌。
				
###### 客户端模式（client credentials）
适用于没有前端的命令行应用，即在命令行下请求令牌。

grant_type=client_credentials

详细流程
1. 第三方Web应用在命令行向 认证服务器 发出请求
```bash
https://oauth.b.com/token?
  grant_type=client_credentials& #通过客户端模式
  client_id=CLIENT_ID& #web应用id
  client_secret=CLIENT_SECRET #web应用密钥，用以确认web应用的身份
```
						
2. 认证服务器验证通过以后，直接返回token
				
##### 7.如何使用token
【向服务端请求token】
http://服务端地址/oauth/token

访问资源时在请求里加上“access_token=token值”即可

【向服务端发送token】
1. 将token放到HTTP请求头的“Authorization”中。（推荐）

2. 将token放到URL的参数中。
示例：localhost:8080/admin/hello?access_token = 123123123123....

##### 8.token过期后
刷新token（获取新的未过期的token）
```bash
https://b.com/oauth/authorize?
  grand_type=refresh_token&  //表示用刷新token模式
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET&
  username=123&
  password=123&
  scope=read
```

#### token的认证机制
token有不同的认证机制。不同的机制对应不同格式的token。
认证机制包括：
* `HTTP Basic Auth`：用户名密码认证，适合测试接口用，不然每次都需要用户名密码很麻烦？
* `Cookie Auth`：Session ID认证，基于Cookie（不允许跨域），需要服务端存储Session ID
* `OAuth`：OAuth认证，社交类APP常用（微信、QQ、微博等）
* `Token Auth`：Token Auth认证，比Session ID安全且无需基于Cookie（即允许跨域），服务端无需保存，有标准化方案支持：JWT

##### 1.什么是jwt
Json Web Token，一种token认证的行业标准。参考了数字签名的原理，以保证token的安全性。

用户验证信息会以json字符串格式加密并保存。

json字符串又分为两部分：头部（header)、载荷（payload)。

**头部：使用哪种加密算法（header)**
```json
{
  'typ': 'JWT', //声明类型（JWT）
  'alg': 'HS256' //alg 声明数字签名的加密算法（HMAC、SHA256等）
}
```

**载荷：用户的验证信息（payload)**
```json
{  
  “iss”: “Online JWT Builder”,  //签发者（iss）
  “iat”: 1416797419,  //签发时间（iat）
  “exp”: 1448333419,  //过期时间（exp）
      ……. 
  “userid”:10001 
} 
```

在将json字符串转为jwt的过程中，还需要生成一个数字签名（signature)附在后面。

**签证、签名（signature)**
```
将 "头部的base64加密字符串 . 载荷的base64加密字符串"与 密钥做头部指定的数字签名算法，得到签证
```

> 常见的数字签名算法有：HMAC算法、RSA算法、SHA256算法等。


然后将头部、载荷、签证先用base64（一种对称加密算法）加密，再用 . 分隔开。

最终，jwt的格式如下：

**头部的base64加密字符串 . 载荷的base64加密字符串 . 签证的base64加密字符串**


> 想了解更多jwt的知识，可参考官网：jwt.io

##### 2.如何使用jwt进行用户验证
依赖jjwt框架。
> 其他Java的jwt框架，可参考官网：jwt.io

###### 生成jwt
```java
//在Java服务端，通过jjwt框架来生成jwt的示例代码
JwtBuilder builder = Jwts.builder().setId("8888") //id是标准字段，还有其他标准字段
     .claim("appname","dyz") //自定义字段
     ....;
String token = builder.compact();

```

###### 解析jwt
```java
//传进来的jwt值
String token ="asdfai13jalkj...";
//解析jwt的内容
Claims claim=Jwts.parser().setSigningKey("xxxx") //密钥
  .parseClaimsJws(token)  
  .getBody(); //jwt的载荷
System.out.println(claim.getId()) //返回载荷中的id（标准字段）
System.out.println(claim.get("appname")) //返回载荷中的appname（自定义字段）
```

###### 校验jwt过期时间
如果jwt里面带上了过期时间，那么在解析jwt时，如果发现jwt已过期，则不允许解析并抛出异常。


## Java常用的安全框架
* Spring Security
* Apache Shiro





