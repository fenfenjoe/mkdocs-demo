# Python3

## 参考资料

官方文档:[https://docs.python.org/zh-cn/3/index.html](https://docs.python.org/zh-cn/3/index.html)
python内置函数：[https://docs.python.org/zh-cn/3/library/functions.html](https://docs.python.org/zh-cn/3/library/functions.html)

## 环境搭建

##### 安装嵌入式python(embedable)
安装python
安装pip
查看pip已安装的模块
	python -m pip list



## 基础

### 数据结构

```python
#### 列表（等于 数组）
mylist = [] #定义
mylist = list() #定义
mylist = ['a','b'] #定义
 #列表切片
mylist[1:3] #第1到第3个元素
mylist[1:] #第1到最后一个元素
mylist[-1] #最后一个元素
#添加
mylist.append('c')
mylist.insert(1,'d')
#删除
mylist.pop(1) #删除第2个元素
mylist.remove('abc') #删除值为abc的元素，若有多个也只删除一个


#### 元组（等于 只可读的列表）
digit = (0, 1, 'two') #定义
tuple([0, 1, 'two']) #列表转元组

#### 字典（等于 Map） 
empty_dict = {'dad':'homer', 'mom':'marge'} #定义
empty_dict = dict('dad':'homer', 'mom':'marge') #定义
'mom' in family #是否有该键
empty_dict.keys() #返回所有键的列表
empty_dict.values() #返回所有值的列表
empty_dict.items() #返回键值对的列表，键值对由元组组成

#### 集合（等于 Set）
empty_set = set()  #定义
myset ={'python', 'r', 'java'} #定义
set(['cobra', 'viper', 'python'])#列表转集合
 #集合运算
add() #增
remove() #删
setA & setB #交集
setA | setB #并集 
setA-setB #差集

#### 函数
def myfunc(context) : 
    a='1'  #缩进4格
    return a #可返回两个值；以元组返回两个值

#### 字符串
str = 'hello'
 #格式化
'raining %s and %s' % ('cats', 'dogs')
'raining {} and {}'.format('cats', 'dogs')
 #查找下标
str.find('abc')
 #取子字符串
str[0] #第一个字符
str[0:3] #取第1~4个字符串
str[-1] #取最后1个字符串
str * 2 #取str并重复1次
str[2:] #取第3个以后所有字符

```

#### 语法

```python
#### 语法
#for循环遍历
for fruit in fruits: #for列表循环
for key, value in family.items(): #for字典循环
for i in range(5) #数字循环（0,1,2,3,4）
for i in range(5,9) #数字循环，指定从5开始（5,6,7,8）
for i in range(5,9,2) #数字循环，指定从5开始，指定步长为2（5,7）
#while循环遍历
while count < 5: #while循环
#if 条件语句
if a==0:
	print(0)
elif a==1:
    print(1)
else :
    pass #若不需要操作，则使用pass关键字，不能空着
#换行：需要加"\"
total = item_one + \
item_two + \
item_three
#等待用户输入
input("按下 enter 键后退出。")
#函数
len() #列表or元组 元素个数
#匿名函数
sum = lambda arg1, arg2: arg1 + arg2
#调用： sum(5,10) #输出15

#### 模块
#导入模块
#导入整个模块
import somemodule
#导入某些函数
from somemodule import firstfunc, secondfunc, thirdfunc
```



#### 库模块

#####		导入新的python库
https://pypi.org/project/ 找需要的库及版本
放到python的Scripts目录中
进入cmd，到python/Scripts目录下，执行 pip install [python库文件名]

#####		常用库

* math：提供数学相关的函数
* pd（Pandas）：数据分析库
* np(numpy)：多维数组
* matplotlib：二维绘图
* scipy：
* sidekick：机器学习
* vn.py
* PyCTP
* QuickLib
* Zipline
* pyecharts：绘图



```python
#查看python所有库
pip list
```



##### Pandas

数据结构及其用法

```python
'''
Series
'''
#定义：由一个一维数组、以及该一维数组的索引组成

#初始化
#用默认索引（0,1,2,3 ...）
s = pd.Series(['a',2,'abc'])  
#自定义索引
s = pd.Series(['a',2,'abc'] , index = ['indexA','indexB','indexC']) 
#查看某个值
s[1]
#去重
s.unique()
					
'''
DataFrame
'''
#定义：由多个Series组成，index可理解为”索引、主键“，columns理解为”列名“

#初始化
#以values二维数组、主键list、列名list
#作为初始化参数
dates = pd.date_range('20130101', periods=6)
df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD')) #np为numpy
#以字典（dict）作为初始化参数
df = pd.DataFrame( {A: 1 ,
                    B: pd.Timestamp('20130102') ，
                    C:pd.Series(1, index=list(range(4)), dtype='float32') } )   #A、B、C为columns
#拷贝
df2 = df.copy()
#查询
#查看前五行数据
df.head(5)
df[0:5]
#查看后三行数据
df.tail(3)
#通过切片方式查询
df.loc[0:3,['A','B']] #返回前三行的AB两列数据
df.iloc[0:3,[0:1]] #返回前三行的AB两列数据
#通过索引值查询
df.loc['20130102':'20130104', ['A', 'B']]  #查询AB两列、索引值为该时间段之间的数据
#返回某一列数据
df['A']  #返回A列所有数据 
#返回某一列某个值
df.iat[1, 1] #返回第二列第二个值
df.iloc[1, 1] #返回第二列第二个值
df.at[dates[1], 'B'] #返回第二列第二个值
#条件查询
df2 = df[df.A > 0]  #返回A的值>0的行
df2 = df[df > 0] #返回df中>0的值
df2 = df[df['E'].isin(['two', 'four'])]  #返回E的值等于two 或four的行
#赋值
df.iat[0, 1] = 0  / df.iloc[0, 1] = 0
df.at[dates[0], 'A'] = 0  /  df.loc[dates[0], 'A'] = 0
df['F'] = s1
df2[df2 > 0] = -df2

#查看信息
#显示列名、显示索引、显示每列的数据类型
df.index
df.columns
df.dtypes
#数据总体统计
df.describe()
#返回：
count #非空值数  
mean  #平均值
std	#标准差  
max  #最大值
min  #最小值  (25%、50%、75%)分位数

#处理
#行变列、列变行
df.T
#删除含缺失值的列
df.dropna(how='any')
#填充缺失值
df.fillna(value=5)
#合并数据（Union）
pieces = [df[:3], df[3:7], df[7:]]        
pd.concat(pieces)
#连接数据（Join）
pd.merge(left, right, on='key')  #left、right均为dataframe，on表示以哪个列进行连接
#插入数据（Insert）
s = df.iloc[3]		
df.append(s, ignore_index=True)
#分组（Group By）
df.groupby('A').sum()

#输入输出
#将数据输出到excel
df.to_excel(r'D:\自动化\web\unittest\DataTest1.xls',sheet_name='Sheet1')
#读取csv的数据
df = pd.read_csv('./train.csv')

#排序
#根据index、列名排序
df.sort_index(axis=1, ascending=False)  
#axis:0根据index 1根据列名  ascending:true升序 false降序
#根据行数据、列数据排序
df.sort_values(by='行名/列名',axis=1/0,ascending=False)
```


​			

##### np(numpy)

```python
np.random.rand(d0,d1..dn) #返回一个d0*d1*...dn的n维数组
np.random.randn(d0,d1..dn) #返回一个n维数组，且数据具有正态分布
```

##### matplotlib

```python

```

##### scipy

```python

```



##### os（操作系统）
```python
List os.listdir(path) #【指定的文件夹包含的文件或文件夹的名字的列表】
os.rename(src, dst) #【重命名文件或目录】
os.rmdir(path) 
os.removedirs(path)
os.remove(path)
```





##### re（正则表达式）

