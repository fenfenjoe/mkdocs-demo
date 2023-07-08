# numpy

是一个python数据结构包。

```python
import numpy as np 

'''
多维数组定义：np.array
'''

# 定义一维数组
a = np.array([0,1,2,3,4])
print(a) #[0,1,2,3,4]

# 定义二维数组
b = np.array([[0,1],[2,3],[4,5]])
b = np.array(object = [[0,1],[2,3],[4,5]])
print(b) #[[0,1],[2,3],[4,5]]

# 定义数组的最小维度
c = np.array([0,1,2,3,4], ndmin = 2)
print(c) #[[0,1,2,3,4]]

# 定义数组中的元素类型
d = np.array([1,2,3], dtype = complex) #
print(d) #1.+0.j 2.+0.j 3.+0.j


```



