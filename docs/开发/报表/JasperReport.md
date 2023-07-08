# JasperReport 

一个提供报表开发功能的JAR包。

可以开发出多种形式的报表，包括：HTML、PDF、JPG等等。


### 参考资料

JasperReport中文教程：[https://www.wenjiangs.com/doc/acyoypdy](https://www.wenjiangs.com/doc/acyoypdy)
【推荐】JasperReport中文手册：[https://www.docin.com/p-226443564.html](https://www.docin.com/p-226443564.html)


### 使用JasperReport框架设计一张报表的流程

* 设计报表
    * 创建一个Jasper XML模板文件（.jrxml）
    * 配置参数、数据源
    * 根据报表的需求，往模板里面添加需要的元素（文字、图片、表格等等）
    * 预览效果
* 调用报表
    * 将Jasper XML文件(.jrxml)编译生成Jasper文件（.jasper），并将Jasper文件放到JAVA项目中
    * 通过编程的方式调用Jasper文件，填充数据，最后生成想要的报表（HTML形式、PDF形式等等）


### 如何开始设计报表？

两种方向：
1. 可视化开发（使用软件：TIBCO JasperSoft Studio 或者 iReport）【推荐】
    * 优点：可视化开发，容易上手；还提供将Jasper XML模板编译成Jasper文件的功能。

2. 编程开发（创建JAVA项目，引入Jasper的JAR包进行开发）
    * 对框架的原理、概念这些更清晰


### 报表的结构

一张报表被分为以下几个区域：

* title
* pageHeader
* columnHeader
* detail
* columnFooter
* pageFooter
* summary


### TIBCO JasperSoft Studio

#### 下载&安装
略

#### 制作表格的不同方法
假设数据源如下：
```json
{
    "name":"dyz",
    "detail":[
        {
            "data1":"1",
            "data2":"2"
        },
        {
            "data1":"1",
            "data2":"2"
        },
        {
            "data1":"1",
            "data2":"2"
        }
    ]
}
```




