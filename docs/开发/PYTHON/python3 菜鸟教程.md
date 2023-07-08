```python
#%% 推导式（类似于Java8 的Stream API，可对集合进行转换、排序、过滤等处理）

'''
列表（List）推导式（返回一个新的List）
'''
names =['Amy','Bob','Alice']
#推导式1：将List所有元素变成大写
new_names = [name.upper() for name in names] 
print(new_names) #['AMY','BOB','ALICE']

#推导式2：将List所有元素变成大写，并过滤出长度大于3的元素
new_names = [name.upper() for name in names if len(name)>3]
print(new_names) #['ALICE']



'''
字典（Map）推导式（返回一个新的Map）
'''
names =['Amy','Bob','Alice']
#推导式1：以List中的值为key，值的长度为value，构造一个新的Map
new_names = {name:len(name) for name in names}
print(new_names) #{'Amy':3,'Bob':3,'Alice':5}

#推导式1：以List中的值为key，值的长度为value，构造一个新的Map
new_names = {name:len(name) for name in names if len(name)>3}
print(new_names) #{'Alice':5}


'''
集合（Set）推导式（返回一个新的Set）
'''
names =['Amy','Bob','Alice']
#推导式1
new_names = {name for name in names}
print(new_names) #{'Amy','Bob','Alice'}


'''
元组（只读列表，Tuple）推导式（返回一个新的Tuple）
'''
names =['Amy','Bob','Alice']
#推导式1
new_names = (name for name in names)
print(new_names) #('Amy','Bob','Alice')
```





```python
#%% operator模块：运算函数，加减乘除、大于小于等于
import operator

dir(operator) #查看operator模块的所有函数

operator.lt(x,y) #小于（lower than） 
operator.gt(x,y) #大于（greater than）
operator.eq(x,y) #等于（equals）
operator.ne(x,y) #不等于（not equals）
operator.le(x,y) #小于等于
operator.ge(x,y) #大于等于

#加减乘除等略

```

```python
#%% math模块：正弦余弦
import math

dir(math) #查看math模块的所有函数

#数学常量
math.e #欧拉数(2.7182...)
math.pi #圆周率PI = 3.1415926...
math.tau #数学常量τ = 6.283185...
#...

#常用函数
math.cos(x)
math.sin(x)
math.tan(x)

math.ceil(x) #对x四舍五入，往上进1。如math.ceil(1.1)=2
math.floor(x) #对x四舍五入，向下取整。如math.ceil(1.1)=1

math.fabs(x) #取x的绝对值
math.gcd(x) #最大公约数
math.sqrt(x) #平方根
math.fmod(x,y) #求x/y的余数

```

```python
#%% 字符串

#格式化字符串
print('我叫 %s，今年 %s 岁' % ('小明',10))  #输出“我叫小明，今年10岁
'''
%s 格式化字符串
%d 格式化整数
%f 格式化浮点数（可指定小数点后精度，如%2f、%3f）
...
'''

print('我叫{}'.format('小明'))
print('我叫{name}，今年{age}岁'.format(name='小明',age=10))
print('我叫{0}，今年{1}岁'.format('小明',10))
print('圆周率={0:.3f}'.format(math.pi)) #浮点数取3位小数
print('{name:10} ===> {age:10}'.format(name='小明',age=10)) #后面的:10保证至少有10的宽度



#多行字符串（三个双引号）
str = """这是一个多行字符串实例
哈哈哈
怎么样
"""

#f-string（新的格式化字符串）
my_name = 'john'
my_map = {'age':10,'parent':'Smith'}
print(f'my name is {my_name}')
print(f'I am {my_map["age"]} years old.My parent is {my_map["parent"]}')

#字符串常用函数
len(x) #返回字符串x的长度
x.find(y) #检查子字符串y是否在x中，若存在则返回索引值，不存在返回-1
isalpha(x) #检查是否都是字母或中文，是则返回true
isdigit(x)、isnumeric(x) #检查是否都是数字，是则返回true
```

```python
#%% pass语句
#pass不作任何操作，只用于占位。如空类、空循环。
while true:
    pass

class Test:
    pass


```

```python
#%% 函数
#定义函数（无返回值）
def test1(arg1): 
	print(arg1)
    
#定义函数（有返回值）
def test2(arg1): 
	print(arg1)
    return arg1
test2(arg1=1) #通过关键字指定入参，打印1

#定义函数（入参有默认值）
def test3(arg1=0): 
	print(arg1)
test3() # 0

#定义函数（arg2为不定长参数，实质是传入一个元组）
def test4(arg1,*arg2): 
	print(arg1)
    print(arg2)
test4(10,20,30,40) #arg1=10,arg2=(20,30,40)


#定义函数（arg2为不定长参数，实质是传入一个字典）
def test5(arg1,**arg2):
    print(arg1)
    print(arg2)
test5(10,{'a':20,'b':30}) #10 {'a':20,'b':30}

#定义函数（通过lambda表达式定义匿名函数）
x = lambda a : a+10 #其中，a为入参，a+10为函数体
print(x(5)) #15

x = lambda a,b : a+b #其中，a,b为入参，a+b为函数体
print(x(1,2)) #3
```



```python
#%% 模块
import sys #导入sys模块，并通过sys调用其函数
sys.argv

from sys import argv #导入sys中的某个函数，并直接使用
argv

#__name__ 属性
#每个模块都有__name__属性
#当__name__=='__main__'时，表明当前模块是自身在运行
#当__name__!='__main__'时，表明当前模块是被引用了

dir(sys) #查看sys模块中所有定义的名称（返回一个List）
dir() #返回当前模块中所有定义的名称


```



```python
#%% 包
# 包是模块的集合，相当于是一个命名空间，可避免模块重名
# 包与模块的结构如下：
# /api   #包名
#   /__init__.py  #每个包必须要有的初始化脚本
#   /test1.py  #test1模块
#   /sub-api   #子包
#     /__init__.py 
#     /test1.py  #子包里的test1模块
#	  /test2.py 

#导入包中的模块
import api.test1,api.sub-api.test2
```



```python
#%% 文件操作
#打开文件
f = open(filename,mode) 
f = open('/tmp/foo1.txt','w')
# mode: 
# r(只读) rb(二进制格式只读) r+(读写) rb+   注意：若文件不存在，r不会创建文件
# w(只写) wb w+ wb+                      注意：文件不存在会新增；会覆盖原有文件
# a(追加) ab a+ ab+                      注意：文件不存在会新增；在原有内容后追加

#关闭文件
f.close()

#写入内容
f.write('哈哈哈')

#读取所有内容
str = f.read()

#读取一行内容
str = f.readline()

#读取所有行内容（返回List<String>）
strs = f.readlines()

#with：预定义的清理行为，会自动关闭文件
with open('file.txt') as f:
    for line in f:
        print(line,end="")
        
```



```python
#%% pickle模块:序列化
#序列化：python中的对象 => 可存储的数据 => 文件
#反序列化：文件 => 可存储的数据 => python中的对象

import pickle

#序列化
my_map = {'a':123 ,'b':'hahahaha'}
f = open('/test.txt','wb')
pickle.dump(my_map,f)
f.close()

#反序列化
f = open('/test.txt','rb')
my_map = pickle.load(f)
print(my_map)
f.close()
```



```python
#%% 异常处理
'''
有哪些系统自带的异常？
ZeroDivisionError 0作为了除数
NameError 未定义的属性
TypeError 类型不一致（比如'2'+2）
'''

#处理单个异常
try:
    x = '2'+2
except TypeError as err:
    print('类型错误！')

#处理多个异常
try:
    x = '2'+2
    y = 1/0
except (TypeError,ZeroDivisionError):
    print('系统异常！')
    
try:
    x = '2'+2
    y = 1/0
except TypeError as typerr: #针对TypeError
    pass
except NameError:
    raise Exception('我是别的异常') #抛出自定义的异常
except: #针对其他异常
    raise #不处理，抛出给上层
else:  #没有抛错误则执行
    print('没有异常')
finally: #抛不抛错误都会执行
    print('无论如何都会执行')


```



```python
#%% 类

#定义类
class Test: #类名：Test
    a = 0 #基本属性
    b = ''
    __w = 0 #私有属性（private），只能在类中访问，外部不能访问，通过"__"开头
    
    def __init__(self): #构造函数，第一个入参必须是当前类的一个实例
        pass
    
    def test(self,arg0,arg1): #实例函数，第一个入参必须是当前类的一个实例
        self.a = arg0
        self.b = arg1
        print('ccc')
        
    def __privateTest(): #私有方法，只能在类中访问
        print('ddd')

#实例化类
my_test = Test()
my_test.test(10,20)
print(my_test.a)

#定义子类
class SubTest(Test):
    def subtest(self):
        print('aaa')
    def test(self,arg0,arg1): #重写父类的方法
        print('bbb')
        
#多继承
class SubSubTest(Test,SubTest):
    pass


#调用父类的方法（不怕被重写）
sub = SubTest()
sub.test(10,20)
super(SubTest,sub).test(10,20) #输出ccc


#类的其他私有方法：
__del__ #析构函数？
__repr__  #toString
__setitem__ #setter方法
__getitem__ #getter方法
__len__ #返回长度
__add__ #重载 加法运算
__sub__ #重载 减法运算

```



```python
#%% 其他模块

#os模块：操作系统接口
	import os
    #返回当前工作目录
    str = os.getcwd()

    #执行系统命令
    os.system('ls')

#re模块：正则表达式
	import re
	re.findAll(r'[0-9]*','dyz 01235 0') #返回['01235','0']
    
#random模块：生成随机数
	import random
    nums = random.sample(range(0,100),10) #从0~100中生成10个随机数
    num = random.random() #从0~1中生成1个随机数
    
#urllib模块：发送网络请求 & smtplib模块：发送邮件请求


#datetime模块：日期函数
	import datetime

    now = datetime.date.today() #返回一个date对象，表示今天（不会具体到时分秒）

    now = datetime.date.fromtimestamp(timestamp) #返回时间戳对应的date对象

    now.year #返回date对象的年、月、日
    now.month
    now.day 

    datestr = now.isoformat() #返回格式如'YYYY-MM-DD’的字符串；

    datestr = now..strftime(fmt) #自定义格式化字符串。

    yesterday = datetime.date.today() - datetime.timedelta(days=1)  #日期计算


    nowtime = datetime.datetime.today() #返回一个datetime对象，表示今天（具体到时分秒）

    nowtime.year、month、day、hour、minute、second、microsecond、tzinfo #年、月、日、时、分、秒、毫秒、时区

    nowtime.date() #datetime转换为date
    nowtime.time() #datetime转换为time
```

