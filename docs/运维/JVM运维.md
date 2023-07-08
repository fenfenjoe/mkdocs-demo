# JVM运维

### 远程调试实战

> 调试前，需要保证服务器的代码与本地的一致

1. 在微服务的启动脚本前，加上以下JVM参数：
```
#address是端口号
-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005
```

2. 点击IDEA的 菜单栏 -> run -> edit configurations... ，点击左上角的+号，添加一个Remote JVM Debug

3. host填写服务器ip、port对应JVM参数中的address（即端口），最后选择对应哪个本地项目

4. 使用debug运行你新建的“Remote JVM Debug"，并在代码中打上断点

5. 对服务器发起请求，若请求成功，此时你就能在IDEA上看到已经停在断点上。



### JVM调参实战



### Arthas:生产环境问题排查工具实战

