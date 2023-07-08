# matplotlib

是一个python画图工具包。


```python
#画单个图、画折线图
import matplotlib
import numpy as np


# 画二维折线（x轴、y轴）
matplotlib.pyplot.plot([点的x轴坐标数组],[点的y轴坐标数组],[折线的样式（颜色、样式），可选])

#示例：
matplotlib.pyplot.plot([1,10],[3,8])
#画出一条直线，由两个点连接而成：(1,3)和(10,8)

matplotlib.pyplot.plot([3,8])
#不传x轴坐标数组，则x轴默认是0,1,2,...
#最后会画出一条直线，由两个点连接而成：(0,3)和(1,8)

matplotlib.pyplot.plot([3,8],'r')
#线条图形同上，但是线条颜色是红色（r:red,b:blue,g:green...）

matplotlib.pyplot.plot([3,8,10,12],marker = 'o')
matplotlib.pyplot.plot([3,8,10,12],marker = matplotlib.marker.DOT)
#画出一条折线，并每个点显示为一个实心圆(o:实心圆,*:星号)

matplotlib.pyplot.title('我是标题')
#定义标题

matplotlib.pyplot.xlabel('我是x轴标签')
matplotlib.pyplot.ylabel('我是y轴标签')
#定义x轴y轴标签

matplotlib.pyplot.grid()
#显示网格线


# 画多条二维折线
matplotlib.pyplot.plot(
    		   [第1条线x坐标数组],[第1条线y坐标数组],[折线的样式（颜色、样式），可选],
               [第2条线x坐标数组],[第2条线y坐标数组],[折线的样式（颜色、样式），可选],
    			...
               )


# 展示图表
matplotlib.pyplot.show()
```



```python
#画多个图
import matplotlib
import numpy as np


#subplot()
matplotlib.pyplot.subplot(2,3,1) #将界面分成2X3的表格，左上角为1，右下角为6，现在我们在1号图开始画
matplotlib.pyplot.plot([2,5,8])
matplotlib.pyplot.title('plot 1')


matplotlib.pyplot.subplot(2,3,2) #在2号图开始画
matplotlib.pyplot.plot([3,6,9])
matplotlib.pyplot.title('plot 2')


matplotlib.pyplot.subtitle('All plot')#主标题


# 展示图表
matplotlib.pyplot.show()



#subplots()
#略
```



```python
#画散点图
import matplotlib
import numpy as np

matplotlib.pyplot.scatter([x坐标的数组],[y坐标的数组],...)
#s:点的大小，默认20，可以是数组，表示每个点的大小
#c:点的颜色，默认蓝色b
#marker:点的样式，默认小圆圈o
#...

matplotlib.pyplot.scatter([3,5],[4,6],s=25,c='r') #(3,4),(5,6)两个红色点，25size

matplotlib.pyplot.show()
```



```python
#画柱状图
import matplotlib
import numpy as np

matplotlib.pyplot.bar([x坐标数组],[柱高数组],[柱宽数组],bottom=底坐标（默认0）)

matplotlib.pyplot.bar(['John','Amy'],[10,20]) #新建一个柱状图
matplotlib.pyplot.bar(['John','Amy'],[10,20],width=0.5)#新建一个柱状图，柱宽为0.5

matplotlib.pyplot.barh(['John','Amy'],[10,20],height=0.5)
#新建一个柱状图，柱宽为0.5，柱子方向是水平方向


```



```python
#画饼图
import matplotlib
import numpy as np

matplotlib.pyplot.pie([扇形面积数组],...)
#explode:数组，每个扇形间的间隔
#labels:数组，每个扇形的标签
#colors:数组，颜色

matplotlib.pyplot.pie([35,25,15,25]) #新建一个饼图

matplotlib.pyplot.pie([35,25,15,25],explode=[0,0.2,0,0]) #突出显示第二个扇形

matplotlib.pyplot.pie([35,25,15,25],autopct='%.2f%%') #百分比，保留2位小数

```

