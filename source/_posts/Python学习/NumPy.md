---
title: NumPy学习
date: 2020-05-05 15:39:45
categories: 
- 计算机基础
- Python
tags:
- Numpy
- Python
toc: true
---
# ndarray数组的创建
首先导入numpy包：
>import numpy as np

通过np.+Tab键查看可使用的函数，在对应函数加上?，再运行，就可以很方便地看到如何使用函数的帮助信息。NumPy封装了一个新的数据类型ndarray（N-dimensional Array），它是一个多维数组对象。该对象封装了许多常用的数学运算函数，方便我们做数据处理、数据分析等，生成方式包括：
* 从已有数据中创建；
* 利用 random 创建；
* 创建特定形状的多维数组；
* 利用 arange、linspace 函数生成等。

## 从已有数据中创建数组
对Python的基础数据类型（如列表、元组等）进行转换来生成 ndarray：
* 将列表转换成ndarray
```python
import numpy as np
ls1 = [10, 42, 0, -17, 30]
nd1 =np.array(ls1)
print(nd1)
[ 10  42   0 -17  30]
print(type(nd1))
<class 'numpy.ndarray'>
```
* 嵌套列表转换成多维ndarray
```python
import numpy as np
ls2 = [[8, -2, 0, 34, 7], [6, 7, 8, 9, 10]]
nd2 =np.array(ls2)
print(nd2)
print(type(nd2))
'''
[[ 8 -2  0 34  7]
[ 6  7  8  9 10]]
<class 'numpy.ndarray'>
'''
# 可以将列表换成元组
```
## 利用random模块生成数组
| 数                | 描述                 |
| ----------------- | -------------------- |
| np.random.random  | 生成0到1之间的随机数 |
| np.random.uniform | 生成均勻分布的随机数 |
| np.random.randn   | 生成标准正态的随机数 |
| np.random.randint | 生成随机的整数       |
| np.random.normal  | 生成正态分布         |
| np.random.shuffle | 随机打乱顺序         |
| np.random.seed    | 设置随机数种子       |
| random_sample     | 生成随机的浮点数     |
```python
import numpy as np
nd3 =np.random.random([4, 3])  #生成4行3列的数组
print(nd3)
print("nd3的形状为：",nd3.shape)
'''
[[0.83239801 0.74702617 0.04089187]
 [0.90023902 0.91950877 0.38947664]
 [0.76919904 0.90359661 0.38207995]
 [0.26542428 0.89554719 0.5686561 ]]
nd3的形状为： (4, 3)
'''
```
## 创建特定形状的多维数组
|函数|	描述|
|np.zeros((3, 4))	|创建 3×4 的元素全为 0 的数组|
|np.ones((3, 4))	|创建 3×4 的元素全为 1 的数组|
|np.empty((2, 3))	|创建 2×3 的空数组，空数据中的值并不为 0，而是未初始化的垃圾值|
|np.zeros_like(ndarr)	|以 ndarr 相同维度创建元素全为  0数组|
|np.ones_like(ndarr)	|以 ndarr 相同维度创建元素全为 1 数组|
|np.empty_like(ndarr)	|以 ndarr 相同维度创建空数组|
|np.eye(5)	|该函数用于创建一个 5×5 的矩阵，对角线为 1，其余为 0|
|np.full((3,5), 10)	|创建 3×5 的元素全为 10 的数组，10 为指定值|

实例如下：
```python
>>> nd7 = np.eye(4)
>>> nd8 = np.diag([1, 8, 3, 10])
>>> nd7
array([[1., 0., 0., 0.],
       [0., 1., 0., 0.],
       [0., 0., 1., 0.],
       [0., 0., 0., 1.]])
>>> nd8
array([[ 1,  0,  0,  0],
       [ 0,  8,  0,  0],
       [ 0,  0,  3,  0],
       [ 0,  0,  0, 10]])
```
可以把生成的数据保存到文件中，如下：
```python
import numpy as np
nd9 = np.random.random([3, 5])
np.savetxt(X=nd9, fname='./data.txt')
nd10 = np.loadtxt('./data.txt')
print(nd10)
```
## 利用 arange() 和 linspace() 函数生成数组
arange()是numpy模块中的函数，其格式为：
>arange([start,] stop[,step,], dtype=None)

其中，start与stop用来指定范围，step用来设定步长。在生成一个ndarray时，start默认为0，步长step可为小数。Python有个内置函数range，其功能与此类似。
```python
>>> print(np.arange(9,-1,-1))
[9 8 7 6 5 4 3 2 1 0]
```
linspace()也是numpy模块中常用的函数，其格式为：
>np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None)

linspace()可以根据输入的指定数据范围以及等份数量，自动生成一个线性等分向量，其中endpoint（包含终点）默认为True，等分数量 num默认为50。如果将retstep设置为True，则会返回一个带步长的 ndarray。
```python
>>> print(np.linspace(0, 1, 10))
[0.         0.11111111 0.22222222 0.33333333 0.44444444 0.55555556
 0.66666667 0.77777778 0.88888889 1.        ]
```
# ndarray数据元素的获取
在NumPy中，既可以获取 ndarray 数组的单个元素，也可以获取一组元素（也即切片），这与 Python 中的列表（list）和元组（tuple）非常类似。
```python
>>> nd2=np.arange(25).reshape([5,5])
>>> nd2[1:3,1:3]
array([[ 6,  7],
       [11, 12]])
>>> nd2[:,1:3]
array([[ 1,  2],
       [ 6,  7],
       [11, 12],
       [16, 17],
       [21, 22]])
>>> nd2[(nd2>3)&(nd2<10)]
array([4, 5, 6, 7, 8, 9])
```
获取数组中的部分元素除了通过指定索引标签来实现外，还可以通过使用一些函数来实现，如通过random.choice函数从指定的样本中随机抽取数据。
```python
>>> a=np.arange(1,25,dtype=float)
>>> c3=nr.choice(a,size=(3,4),p=a / np.sum(a))
>>> c3
array([[16., 13., 10., 23.],
       [ 9., 24.,  4., 18.],
       [11., 23., 18., 19.]])
```
# 算术运算
涉及大量的数组或矩阵运算，本节我们将重点介绍两种常用的运算：
* 一种是对应元素相乘，又称为逐元乘法（Element-Wise Product），可以使用 np.multiply() 函数或者*运算符；
* 另一种是点积或内积元素，运算符为np.dot()。
## 对应元素相乘
对应元素相乘（Element-Wise Product）是两个矩阵中对应元素乘积。np.multiply() 函数用于数组或矩阵对应元素相乘，输出与相乘数组或矩阵的大小一致，其格式如下：
>numpy.multiply(x1, x2, /, out=None, *, where=True,casting='same_kind', order='K', dtype=None, subok=True[, signature, extobj])

其中x1、x2之间的对应元素相乘遵守广播规则。
```python
>>> A = np.array([[1,2],[-1,4]])
>>> B = np.array([[2,0],[3,4]])
>>> A*B
array([[ 2,  0],
       [-3, 16]])
>>> np.multiply(A,B)
array([[ 2,  0],
       [-3, 16]])
>>> print(A*2.0)
[[ 2.  4.]
 [-2.  8.]]
 ##数组通过激活函数，输入输出形状一致
 import numpy as np
X = np.random.rand(2, 3)


def softmoid(x):
    return 1 / (1 + np.exp(-x))


def relu(x):
    return np.maximum(0, x)


def softmax(x):
    return np.exp(x) / np.sum(np.exp(x))


print("输入参数X的形状：", X.shape)
print("激活函数softmoid输出形状：", softmoid(X).shape)
print("激活函数relu输出形状：", relu(X).shape)
print("激活函数softmax输出形状：", softmax(X).shape)
'''
输入参数X的形状： (2, 3)
激活函数softmoid输出形状： (2, 3)
激活函数relu输出形状： (2, 3)
激活函数softmax输出形状： (2, 3)
'''
```
## 点积运算
点积运算（Dot Product）又称为内积，在 NumPy 中用 np.dot() 函数表示，其一般格式为：
>numpy.dot(a, b, out=None)
```python
X1=np.array([[1,2],[3,4]])
X2=np.array([[5,6,7],[8,9,10]])
X3=np.dot(X1,X2)
print(X3)
'''
[[21 24 27]
[47 54 61]]
'''
```
# 数组的变形(改变数组形状)
在矩阵或者数组的运算中，经常会遇到需要把多个向量或矩阵按某轴方向合并，或展平（如在卷积或循环神经网络中，在全连接层之前，需要把矩阵展平）的情况。下面介绍几种常用的数组变形方法。
| 函数/属性       | 描述                                                                    |
| --------------- | ----------------------------------------------------------------------- |
| arr.reshape()   | 重新将向量 arr 维度进行改变，不修改向量本身                             |
| arr.resize()    | 重新将向量 arr维度进行改变，修改向量本身                                |
| arr.T           | 对向量 arr 进行转置                                                     |
| arr.ravel()     | 对向量 arr 进行展平，即将多维数组变成1维数组，不会产生原数组的副本      |
| arr.flatten()   | 对向量 arr 进行展平，即将多维数组变成1维数组，返回原数组的副本          |
| arr.squeeze()   | 只能对维数为1的维度降维。对多维数组使用时不会报错，但是不会产生任何影响 |
| arr.transpose() | 对高维矩阵进行轴对换                                                    |
```python
# 测试几个常用的函数
>>> arr = np.arange(10)
>>> arr.reshape(2,5)
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
>>> arr.resize(2,5)
>>> print(arr)
[[0 1 2 3 4]
 [5 6 7 8 9]]
 >>> arr.T
array([[0, 5],
       [1, 6],
       [2, 7],
       [3, 8],
       [4, 9]])
>>> print(arr.ravel('F'))  # 按列展平
[0 5 1 6 2 7 3 8 4 9]
>>> print(arr.ravel())  # 按行展平
[0 1 2 3 4 5 6 7 8 9]
# flatten()函数将矩阵转换成向量
>>> a =np.floor(10*np.random.random((3,4)))
>>> print(a)
[[2. 8. 5. 1.]
 [2. 0. 5. 4.]
 [9. 9. 7. 8.]]
>>> print(a.flatten())
[2. 8. 5. 1. 2. 0. 5. 4. 9. 9. 7. 8.]
# transpose() 对高维矩阵进行轴对换
>>> arr2 = np.arange(24).reshape(2,3,4)
>>> print(arr2.shape)  #(2, 3, 4)
(2, 3, 4)
>>> print(arr2.transpose(1,2,0).shape)  #(3, 4, 2)
(3, 4, 2)
```
# ndarray合并数组
|函数	|描述|
|np.append()|	内存占用大|
|np.concatenate()	|没有内存问题|
|np.stack()	|沿着新的轴加入一系列数组|
|np.hstack()	|堆栈数组垂直顺序（行）|
|np.vstack()	|堆栈数组垂直顺序（列）|
|np.dstack()	|堆栈数组按顺序深入（沿第3维）|
|np.vsplit()	|将数组分解成垂直的多个子数组的列表|

几点说明：
* append()、concatenate() 以及 stack() 都有一个 axis 参数，用于控制数组的合并方式是按行还是按列。
* 对于 append() 和 concatenate()，待合并的数组必须有相同的行数或列数（满足一个即可）。
* stack()、hstack()、dstack() 要求待合并的数组必须具有相同的形状（shape）

```python
>>> a =np.array([[1, 2], [3, 4]])
>>> b = np.array([[5, 6]])
>>> c = np.concatenate((a, b), axis=0)
>>> print(c)
[[1 2]
 [3 4]
 [5 6]]
>>> d = np.concatenate((a, b.T), axis=1)
>>> print(d)
[[1 2 5]
 [3 4 6]]
# stack，沿指定轴堆叠数组或矩阵
>>> a =np.array([[1, 2], [3, 4]])
>>> b = np.array([[5, 6], [7, 8]])
>>> print(np.stack((a, b), axis=0))
[[[1 2]
  [3 4]]

 [[5 6]
  [7 8]]]
```
# Numpy批量处理
如何把大数据拆分成多个批次呢？可采用如下步骤：
* 得到数据集
* 随机打乱数据
* 定义批大小
* 批处理数据集

示例如下：
```python
import numpy as np

data_train = np.random.randn(2000, 2, 3)
print(data_train.shape)
# (2000, 2, 3)
np.random.shuffle(data_train)
# 定义批量
batch_size = 100
# 批处理
for i in range(0,len(data_train),batch_size):
    x_batch_sum=np.sum(data_train[i:i+batch_size])
    print("第{}批次，该批次的数据之和：{}".format(i,x_batch_sum))
'''
(2000, 2, 3)
第0批次，该批次的数据之和：-2.5597045866733144
第100批次，该批次的数据之和：-28.20552534073626
第200批次，该批次的数据之和：-39.954399816952744
第300批次，该批次的数据之和：15.230660353237122
第400批次，该批次的数据之和：3.756517698762309
第500批次，该批次的数据之和：2.416672409682702
第600批次，该批次的数据之和：-47.262073781065844
第700批次，该批次的数据之和：-4.384517680501666
第800批次，该批次的数据之和：0.9617151643255646
第900批次，该批次的数据之和：27.657472571275203
第1000批次，该批次的数据之和：39.91121481922393
第1100批次，该批次的数据之和：-37.02521264944135
第1200批次，该批次的数据之和：-19.19864630328485
第1300批次，该批次的数据之和：37.92424857767804
第1400批次，该批次的数据之和：-17.742587465605805
第1500批次，该批次的数据之和：-9.856616675258884
第1600批次，该批次的数据之和：6.976135672087105
第1700批次，该批次的数据之和：-5.35707792331438
第1800批次，该批次的数据之和：15.930799536487907
第1900批次，该批次的数据之和：-4.654408531448469
'''
```
# ufnc通用函数
NumPy 中的函数可以是向量或矩阵，而利用向量或矩阵可以避免使用循环语句，通用函数如下：
| 函数                   | 使用方法                 |
| ---------------------- | ------------------------ |
| sqrt()                 | 计算序列化数据的平方根   |
| sin()、cos()           | 三角函数                 |
| abs()                  | 计算序列化数据的绝对值   |
| dot()                  | 矩阵运算                 |
| log()、logl()、log2()  | 对数函数                 |
| exp()                  | 指数函数                 |
| cumsum()、cumproduct() | 累计求和、求积           |
| sum()                  | 对一个序列化数据进行求和 |
| mean()                 | 计算均值                 |
| median()               | 计算中位数               |
| std()                  | 计算标准差               |
| var()                  | 计算方差                 |
| corrcoef()             | 计算相关系数             |

```python
# math与numpy函数性能比较
import time
import math
import numpy as np
x = [i * 0.001 for i in np.arange(1000000)]
start = time.clock()
for i, t in enumerate(x):
    x[i] = math.sin(t)
print("math.sin:", time.clock() - start)
x = [i * 0.001 for i in np.arange(1000000)]
x = np.array(x)
start = time.clock()
np.sin(x)
print("numpy.sin:", time.clock() - start)
'''
math.sin: 0.17639699999999991
numpy.sin: 0.017746999999999957
'''
```
## 循环与向量运算
实现计算的向量化，可大大地提高运行速度。NumPy 库中的内建函数使用了 SIMD 指令。如下使用的向量化要比使用循环计算速度快得多。如果使用GPU，其性能将更强大，不过Numpy不支持GPU。
```python
import time
import numpy as np
x1 = np.random.rand(1000000)
x2 = np.random.rand(1000000)
##使用循环计算向量点积
tic = time.process_time()
dot = 0
for i in range(len(x1)):
    dot += x1[i] * x2[i]
    toc = time.process_time()
print("dot = " + str(dot) + "\n for loop----- Computation time = " +
      str(1000 * (toc - tic)) + "ms")
# 使用numpy函数求点积
tic = time.process_time()
dot = 0
dot = np.dot(x1, x2)
toc = time.process_time()
print("dot = " + str(dot) + "\n verctor version---- Computation time = " +
      str(1000 * (toc - tic)) + "ms")
'''
dot = 249965.34253404333
 for loop----- Computation time = 1146.446813ms
dot = 249965.34253405244
 verctor version---- Computation time = 2.297117999999987ms
'''
```
# 广播机制
调整数组使得 shape 一样，需要满足一定的规则，否则将出错。这些规则可归纳为以下 4 条。
* 让所有输入数组都向其中 shape 最长的数组看齐，不足的部分则通过在前面加 1 补齐，如：
>a：2×3×2
b：3×2

则 b 向 a 看齐，在 b 的前面加 1，变为 1×3×2。

* 输出数组的 shape 是输入数组 shape 的各个轴上的最大值。
* 如果输入数组的某个轴和输出数组的对应轴的长度相同或者某个轴的长度为 1 时，这个数组能被用来计算，否则出错。
* 当输入数组的某个轴的长度为1时，沿着此轴运算时都用（或复制）此轴上的第一组值。
>目的：计算A+B，其中A为 4×1 矩阵，B为一维向量 (3,)。

处理过程如下：
* 根据规则 1，B 需要向看齐，把 B 变为 (1,3)
* 根据规则2，输出的结果为各个轴上的最大值，即输出结果应该为 (4,3) 矩阵，那么 A 如何由 (4,1) 变为 (4,3) 矩阵？B 又如何由 (1,3) 变为 (4,3) 矩阵？
* 根据规则4，用此轴上的第一组值（要主要区分是哪个轴），进行复制（但在实际处理中不是真正复制，否则太耗内存，而是采用其他对象如 ogrid 对象，进行网格处理）即可，详细处理过程如图1所示。
<div align=center><img src="/image/Numpy广播.png" width="400"/></div>
 <center>图1 NumPy广播规则</center>

 ```python
import numpy as np
A = np.arange(0, 40,10).reshape(4, 1)
B = np.arange(0, 3)
print("A矩阵的形状:{},B矩阵的形状:{}".format(A.shape,B.shape))
C=A+B
print("C矩阵的形状:{}".format(C.shape))
print(C)
'''
A矩阵的形状:(4, 1),B矩阵的形状:(3,)
C矩阵的形状:(4, 3)
[[ 0  1  2]
 [10 11 12]
 [20 21 22]
 [30 31 32]]
'''
 ```