# Java常用函数



比较常用的通用函数库：

* org.apache.commons（通用工具）
* com.google.guava（通用工具）
* com.google.gson（Json序列化）
* cn.hutool（通用工具）





#### org.apache.commons

提供了大量的通用的、常用的函数，意在减少程序员重复造轮子的情况。



**commons-beanUtils**

提供一些类反射相关方法的封装



**commons-codec**

提供常用的编码和解码工具，如DES、SHA1、MD5、Base64、URL等



**commons-collections**

提供了诸如ListUtils、MapUtils等集合操作工具类



**commons-compress**

解压缩工具

**commons-logging**

日志封装

**CSV**

CSV读写工具



**Configuration**

支持从properties或者xml加载配置信息



**Daemon**

支持将Java进程变成系统的后台服务



**Exec**

支持执行外部进程，例如.exe文件或者命令行



**Net**

封装了各种网络协议的客户端



**Pool**

对象池实现



**Validator**

在XML中定义校验器和校验规则，支持国际化



#### cn.hutool


Excel操作:cn.hutool.poi.excel.ExcelWriter  
Excel操作:cn.hutool.poi.excel.ExcelReader  
ID生成器:cn.hutool.core.util.IdUtil  
字符串工具类:cn.hutool.core.util.StrUtil  