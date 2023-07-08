# IDEA使用宝典
[toc]

### 常用插件
* Maven Helper（方便查看依赖冲突）
* Spring Boot Assistant（yaml自动提示、补全）



### 常见问题

#### ultimate版下载&破解方法
参考：https://zhuanlan.zhihu.com/p/312951091
* 在Settings/Preferences… -> Plugins 内手动添加第三方插件仓库地址：https://plugins.zhile.io
* 搜索：IDE Eval Reset插件进行安装
* 重启后，在Help - Eval Reset可以设置自动重置试用日期


#### 如何初始化配置JDK、Maven
file-project structure-SDK，选择JDK版本


#### new Maven Project时发现只有两个archetype模板
先关闭当前项目；
file-settings-plugins处，查询并安装maven archetypes插件。
file-New Project Setup-setting for new projects-build-build tools-maven处，修改Maven的路径及settings.xml的路径。
重新new Maven Project，发现archetype模板变多。


#### 如何导入Maven Project
file-new-project from existing sources；
选择需要导入的maven项目，next；
导入类型选择maven。


#### Maven Project导入后不知如何运行
右键单击pom.xml-Add as Maven Project。


#### 有时候在无法自动import class
在maven tool view中，右键当前模块-reload project


#### 为spring cloud项目增加热部署功能
参考：https://blog.csdn.net/u011447164/article/details/121991926?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.highlightwordscore&spm=1001.2101.3001.4242.1


#### 在IDEA中部署SpringBoot项目到Docker环境
社区版不支持，需要使用ultimate版

方法一：使用docker-maven-plugin插件（已不推荐）
该方法由Maven在build的过程中创建Docker镜像，无需Dockerfile。


方法二：使用dockerfile-maven-plugin插件
```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>dockerfile-maven-plugin</artifactId>
    <version>1.4.13</version>
    <executions>
        <execution>
        <id>default</id>
        <goals>
            <goal>build</goal>
        </goals>
        </execution>
    </executions>
    <configuration>
        <!--生成的镜像名-->        
        <repository>azil/${artifactId}</repository>
        <!--镜像的版本 -->
        <tag>${project.version}</tag>
        <!--生成的变量，可在Dockerfile中引用 -->
        <buildArgs>
            <JAR_FILE>${project.build.finalName}.jar</JAR_FILE>
        </buildArgs>    
    </configuration>
</plugin>
```



### GIT
查看提交历史

将代码回滚到之前的版本

### 文件颜色

绿色，已经加入版本控制暂未提交
红色，未加入版本控制 
蓝色，加入版本控制，已提交，有改动
白色，加入版本控制，已提交，无改动 
灰色：版本控制已忽略文件


#### 常用快捷键
首先，可以通过file -> settings ->keymap查看快捷键的关联

* 自动导包 Alt + Enter 
* 查看类的结构 Ctrl + F12 
* 返回到上一次访问的界面 Ctrl + Alt + PageUp
* 返回到下一次访问的界面 Ctrl + Alt + PageDown
* 全文检索 Ctrl + Shift + F
* 显示接口的实现类 Ctrl + H
* 显示类可重载的方法 Ctrl + O
* 显示某个函数在哪些地方被引用 Alt + F7 
* 查看git某行代码的最新修改人 git -> annotate with git blame

#### DEBUG快捷键

* F7  ：Step Into，若当前行有方法，则进入
* F8  ：Step Over，直接执行当前行，并跳到下一行（等于Eclipse 的F6）
* F9  ：跳转到下一个断点（等于Eclipse的F8），若没有下一个断点，则运行至结束
* Shift + F8 ：Step Out，跳出当前方法


#### if、for、while代码自动补全
```java
boolean flag = false;
List list = new ArrayList<String>();
//输入flag.if+回车 会自动生成以下代码
if(flag){

}

//输入flag.else+回车 会自动生成以下代码
if(!flag){

}

//输入list.for+回车 会自动生成以下代码
for(String s:list){

}

//输入100.fori+回车 会自动生成以下代码
for(int i=0;i<0;i++){

}

//输入100.forr+回车 会自动生成以下代码
for(int i=100;i>0;i--){

}

//输入flag.while+回车 会自动生成以下代码
while(flag){

}

```

#### System.out.println缩写：sout+回车
```java
//sout+回车 会自动生成以下代码
System.out.println();

```
