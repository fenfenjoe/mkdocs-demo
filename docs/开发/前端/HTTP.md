# HTTP



### HTTP的版本
* http0.9

    【已过时】仅有GET请求；无响应状态；没有报文头，因而仅能识别纯文本内容；无状态

* http1.0

    有POST、GET、HEAD请求；有响应状态；有报文头，支持响应其它格式的内容；支持长连接

    【新增】缓存机制、用户认证机制

* http1.1

    【主流】新增OPTION、PUT、DELETE等方法；默认长连接

    【新增】管道连接；块编码

* http2.0


### http1.1报文详解

**格式**

请求报文：
* 请求方法
    GET
        请求参数放在【请求URL】中
            不安全。参数值直接暴露在URL中
            参数有长度限制。
            特殊字符需要URLEncode转义。
        【通过地址栏发出的请求】都是Get请求
    PUT
        请求参数放在【报文体（消息正文中）】
        【传文件】可用
* URI
* 协议版本
    http/0.9 or 1.0 or 1.1 or 2.0
* 通用报头
    【缓存控制】cache-control
    Transfer-Encoding
    【是否保持TCP连接】connection
    Pragma
* 请求报头
    【希望接收的编码】accept-encoding
    【希望接收的语言】accept-language
    【希望接收的数据格式】accept
    【浏览器类型】user-agent
    【客户端域名】host
    【消息正文格式】content-type
    【报文内容长度】content-length
    【请求来自哪个链接】referer
    【键值】cookie
    Upgrade-Insecure-Requests

响应报文：
* 响应开始行
    协议版本
    状态码及其描述
        1xx 消息
        2xx 成功
        3xx 重定向
        4xx 请求错误
        5xx、6xx 服务器错误
* 消息报头
    通用报头
        【缓存控制】cache-control
        Transfer-Encoding
        【是否保持TCP连接】connection
        Pragma
    响应报头
        【消息正文格式】content-type
        【服务器类型】Server
* 消息正文
			



### Content-Type
	返回的数据类型格式由MIME标准定义，以下是常用的数据类型
	【表单传参默认】application/x-www-form-urlencoded
	【表单传多种格式（参数+文件）】multipart/form-data
	【Json数据】application/json
	【XML】text/xml
	【pdf】application/pdf
	【*所有文件】application/octet-stream
	【jpeg图片】image/jpeg
	【txt】text/plain
	【mpeg视频】video/mpeg


### HTTP Authentication

* Basic Auth （凭用户名/密码登录）
* Bearer Token（凭令牌登录）
* Digest Auth 
* OAuth 1.0（第三方授权）
* OAuth 2.0（第三方授权）

### 跨域

#### 什么是跨域问题

为了安全起见，浏览器设置了同源策略，当页面执行Javascript脚本的时候，浏览器会检查访问的资源是否同源，如果不是，就会报错。  
注意，跨域请求是可以正常发出的，但是浏览器接收到响应报文时，会对响应报文进行是否跨域的判断。

#### 什么是同源策略？怎么判断资源是否同源？

网络资源有HTML、CSS、JS、图片、视频、接口等等类型。  
每个资源都会有它唯一的URL，例如：  
* HTML文件： http://a.com/test.html 
* CSS文件： https://b.com/1.css 
* 图片文件： http://10.17.69.130:8000/me.jpg 
* JS文件：http://10.16.10.10:9000/helloworld.js 
* 接口： http://localhost:8080/test


**当两个资源的URL中的协议、域名、端口号其中一个不一样，则这两个资源是非同源资源**  

**反过来说，只要协议、域名、端口号都一致，则这两个资源是同源资源**  

跨域是非常常见的现象！请求是跨域的但并不一定会报错，普通的图片请求。css文件请求是不会报错的。  
例如，你有如下的一个HTML文件：

```html
<!-- http://a.com/test.html -->
<!DOCTYPE html>
<html>
    <head>
        <link rel="stylesheet" type="text/css" href="http://c.com/mystyle.css">
    </head>
    <body>
        <img src="http://b.com/hello.jpg" />
    </body>
</html>

```
从代码中可见，该HTML在 http://a.com 这个域里，却引用了 http://b.com 的一张图片以及 http://c.com 的一个CSS文件，很明显已经跨域了。
但是，如果这些文件真实存在，是可以成功渲染到这个HTML里的！


因为跨域而加载不到资源、报错的条件是：**JS文件发送Ajax请求访问非同源资源**。  
浏览器允许发出访问非同源接口的请求，但是结果会被浏览器拦截，导致加载不到资源。  

#### 跨域报错的解决方案？

##### JSONP 

利用的是 script 标签 src 属性请求 js 无跨域问题，但具有局限性，只能发送 get 请求

##### CORS（推荐）

前端：
```javascript
   $.ajax({
        url : "http://localhost:8080/test",
        type: "POST",
        
        success : function(result) {
           alert("success");
        },
        //若要跨域，则加上下面两个参数
        crossDomain: true,  
        xhrFields: {        
            withCredentials: true //是否向后端发送cookie
        }
    });

```

后端：
根据实际情况，需要在Response中添加下面几个Header：
|Header|值|说明|
|---|---|---|
|Access-Control-Allow-Origin|"http:localhost:8080" 、 "*" |填url：允许来自该url的请求跨域 填"*"：允许所有的请求跨域|
|Access-Control-Allow-Credentials|true 、 false|是否允许携带cookie|
|Access-Control-Allow-Headers|"header的name"、"*"|允许携带的请求头|
|Access-Control-Allow-Methods|"GET"、"POST"、"*"等|允许的请求方法类型|

浏览器会判断响应中 Access-Control-Allow-Origin 值是否和当前的地址相同，匹配成功后才会做响应处理。

##### 使用Nginx反向代理

使用Nginx反向代理前端和后端，前端通过Nginx访问后端的资源，这样前后端的IP地址一样，便不会出现跨域问题。  

##### web sockets

略


