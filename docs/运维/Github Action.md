# Github Action


## 参考

[CSDN-GitHubActions详解](https://blog.csdn.net/unreliable_narrator/article/details/124468384)  
[Github Action Market](https://github.com/marketplace?type=actions)  


## 概述

GitHubActions是一个Github提供的持续集成和持续交付的平台。于2019年11月后对该功能全面开放，现在所有的github用户可以直接使用该功能。  

GitHub 提供 Linux、Windows 和 macOS 虚拟机来运行您的工作流程，或者您可以在自己的数据中心或云基础架构中托管自己的自托管运行器。  

**当你在仓库的根目录中，添加上.github/workflows目录，并在目录里添加yaml格式的配置文件**  

**那么当你push代码到github时，github检测到你的仓库里有.github目录，便会自动执行你的配置文件，生成虚拟机、按照你的配置搭建环境。**  

等于免去了自己买服务器、自己安装环境的流程！对个人开发者非常友好。  

那么，下面就通过一些使用场景，带大家体现一下GithubAction是否好用。  



## 实战

### 使用Github Action 部署一个简单的Vue应用





### 使用Github Action + mdbook + Markdown 部署个人博客

参考：[https://github.com/Yang-Xijie/mdbook-site](https://github.com/Yang-Xijie/mdbook-site)

#### 1.安装Rust


#### 2.安装mdbook


#### 3.使用mdbook创建项目


#### 4.编写workflows配置文件


#### 5.push代码，github自动部署


#### 6.访问个人博客



### 使用Github Action + mkdocs + Markdown 部署个人博客（推荐）

参考：  
项目示例：[https://github.com/Yang-Xijie/mkdocs-site](https://github.com/Yang-Xijie/mkdocs-site)  
mkdocs官网：[https://www.mkdocs.org/getting-started/#getting-started-with-mkdocs](https://www.mkdocs.org/getting-started/#getting-started-with-mkdocs)  

#### 1.安装python

#### 2.使用pip安装mkdocs
```
pip install mkdocs
```

#### 3.使用mkdocs创建项目
```
mkdocs new my-project
cd my-project
```

#### 4.编写workflows配置文件

添加.github/workflows/PublishMySite.yml文件，内容如下：  

```
# PublishMySite.yml
name: publish site
on: # 在什么时候触发工作流
  push: # 在从本地main分支被push到GitHub仓库时
    branches:
      - master
  pull_request: # 在main分支合并别人提的pr时
    branches:
      - master
jobs: # 工作流的具体内容
  deploy:
    runs-on: ubuntu-latest # 创建一个新的云端虚拟机 使用最新Ubuntu系统
    steps:
      - uses: actions/checkout@v2 # 先checkout到main分支
      - uses: actions/setup-python@v2 # 再安装Python3和相关环境
        with:
          python-version: 3.x
      - run: pip install mkdocs-material # 使用pip包管理工具安装mkdocs-material
      - run: mkdocs gh-deploy --force # 使用mkdocs-material部署gh-pages分支
```


#### 5.push代码，github自动部署

需要注意：
1. Git地址使用SSH地址，不要使用HTTPS的。不然国内push代码老是会timed out；
2. Settings -> Actions -> General -> Workflow permissions ，需要勾选第一个： Read And Write Permission  

#### 6.访问个人博客

https://账号名称.github.io/仓库名称/   



#### 如何更换主题
[https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes)  




### 使用Github Action，当有代码提交时，发送邮件提醒



