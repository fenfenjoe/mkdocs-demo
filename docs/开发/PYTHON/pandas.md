# Pandas



```python
#创建一个DataFrame
data = np.random.randn(6,4)
myindex = pd.date_range('20130101',periods=6)
mycolumns = ['A','B','C','D']
df = pd.DataFrame(data,index=myindex,columns=mycolumns)

###data可以是：
data = np.random.randn(6,4) #ndarray
data = {'Site':['Google', 'Runoob', 'Wiki'], 'Age':[10, 12, 13]}  #字典嵌套列表
data = pd.Series([1,2,3,4,5,6],index=pd.date_range('20130102',periods=6)) #series
data = [{'a': 1, 'b': 2},{'a': 5, 'b': 10, 'c': 20}] #字典列表
data = [1,2,3,4,5,6] #list
data = [[1,'tom','20110101'],[2,'jack','20110102']] #二维数组
```

```python
#列操作

#获取所有列名（返回List）
list(df)
df.columns.values

#获取某列（返回DataFrame），A为列名
df['A']

#获取某列，并转为list


#将已经存在的数据列做相加运算（并生成新的列）
df['four']=df['one']+df['three']

#添加一列
df['E'] = ['1','2','3','4','5']

#添加一列（2）
df['E'] = pd.Series([1,2,3,4,5,6],index=pd.date_range('20130102',periods=6))

#往1位置插入score列
df.insert(1,column='score',value=[91,90,75])

#删除列
df.pop('two')
del df['one']


```

```python
#行操作

#获取所有index
df.index.values

#通过Index获取某行（返回DataFrame）
df['20130104':'20130106']

#获取前几行（返回DataFrame）
df[0:3]


#追加行
df = pd.DataFrame([[1, 2], [3, 4]], columns = ['a','b'])
df2 = pd.DataFrame([[5, 6], [7, 8]], columns = ['a','b'])
df = df.append(df2)

#删除行（index=0的行）
df.drop(0)

```


```python
#值操作

#获取某个单元格的值(index=0,列名为A的值)
df.loc(0,'A')#返回一个series
df.iloc(0,1)

#修改dataframe某个值
df.at['20130104','A'] = 0

#修改dataframe某个值（2）
df.iat[0,1] = 0


#查看前5条数据
df.head(5)

#查看最后5条数据
df.tail(5)

#查找index=5的行



```

```python
#创建一个Series
s = pd.Series([1,3,5,np.nan,6,8])

```