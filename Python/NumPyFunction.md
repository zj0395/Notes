# NumPy数据存取与函数  
## 一维与二维数据  
#### CSV - Comma-Separated Value，逗号分隔值  
- 用来存储一维、二维数据非常方便

#### np.array() 与np.asarray() 区别
- np.asarray() 相当于引用, np.array() 相当于复制
```python
>>> import numpy as np
>>> a=np.random.randint(0,200,(4,5))
>>> a
array([[105,  73,  55, 161,  87],
       [ 66, 136, 128, 141, 108],
       [ 94,  50, 175,  41,  97],
       [167,  72,  27,  24, 127]])
>>> s=np.array(a)
>>> f=np.asarray(a)
>>> a.resize(2,10)
>>> s
array([[105,  73,  55, 161,  87],
       [ 66, 136, 128, 141, 108],
       [ 94,  50, 175,  41,  97],
       [167,  72,  27,  24, 127]])
>>> f
array([[105,  73,  55, 161,  87,  66, 136, 128, 141, 108],
       [ 94,  50, 175,  41,  97, 167,  72,  27,  24, 127]])
```

#### np.savetxt(fname, array, fmt='%.18e', delimiter=None)
- fname : 目的地址，文件、字符串或产生器，也可是.gz 或.bz2 压缩文件
- array : 数据来源
- fmt :  每个元素的格式
- delimiter : 用来分隔的字符串,默认是空格
- 返回值 : 无

#### np.loadtxt(fname, dtype=float, delimiter=None, unpack=False)
- fname : 来源,可以是文件、字符串或产生器，可以是 .gz或.bz2 的压缩文件
- dtype : 数据类型
- delimiter : 文件中的分隔字符串,默认是任何空格
- unpack : 如果为True, 读入属性将写入不同变量
- 返回值 : 新数组

## 多维数据
>会丢失维度的信息 
>可以通过元数据文件来存储额外信息

#### array.tofile(fname, sep='', format='%s')
- fname : 文件、字符串
- sep : 数据分割字符串，如果是空串，那么写入文件为二进制, 肉眼无法识别
- format : 写入数据的格式
- 返回值 : 无

#### np.fromfile(fname, dtype=float, count=-1, sep='')
- fname : 文件、字符串
- dtype : 读取的数据类型
- count : 读入的元素个数，-1表示读入整个文件
- sep : 分割字符串，为空则视要读取的文件为二进制
- 返回值 : 新数组

## 便捷方法
>要使用 numpy 自定义的文件格式, 只能numpy自己用  
>会还原出原数组的维度信息  

#### np.save(fname, array)
#### np.savez(fname, array)
#### np.load(fname)
- fname : 以 .npy 为扩展名,即 numpy 自定以的格式, 压缩的扩展名为 .npz

## 随机数函数
### NumPy的random子库
##### 基本函数
- np.random.rand(d0, d1,,,,dn) 根据 d0-dn 创建随机数数组, d0,,,dn 为维度的信息, 结果为浮点数,\[0,1),均匀分布
- np.random.randn(d0,d1,,,dn) 标准正态分布
- np.random.randint(low,high,shape) 根据shape创建随机整数或整数数组,范围是\[low,high)
- np.random.seed(s) 随机数种子, s是给定的种子值, 影响之后所有随机数的生成

##### 高级函数
- np.random.shuffle(a) 根据数组 a 的第一轴进行随机排列, 原位操作
- np.random.permutation(a) 根据数组 a 的第一轴产生一个新的乱序数组, 非原位操作
- np.random.choice(a, size, replace ,p) 从一维数组 a 中以概率 p 抽取元素, p也是一个概率的数组, 且和应该为1; 形成 size 形状的新数组; replace表示是否可以重用新元素, 默认为True, 即可重复选取

##### 带分布的随机数函数
- 都是浮点数
- np.random.uniform(low, high, size) 均匀数组
- np.random.normal(loc, scale, size) 正态分布的数组, loc均值, scale标准差, size形状
- np.random.poisson(lam, size) 具泊松分布的数组, lam随机时间概率, size形状

## 统计函数
- np.sum(a, axis=None) a为数组,  axis为给定轴, 为空则是全部元素
- np.mean(a, axis=None) 算术平均和
- np.average(a, axis=None, weights=None) 加权平均值
- np.std(a, axis=None) 标准差
- np.var(a, axis=None) 方差

```python
>>> a=np.arange(20).reshape(4,5)
>>> a
array([[ 0,  1,  2,  3,  4],
       [ 5,  6,  7,  8,  9],
       [10, 11, 12, 13, 14],
       [15, 16, 17, 18, 19]])
>>> np.sum(a, axis=1)
array([10, 35, 60, 85])
>>> np.mean(a, axis=0)
array([  7.5,   8.5,   9.5,  10.5,  11.5])
>>> np.mean(a)
9.5
>>> np.average(a)
9.5
>>> np.average(a, axis=0, weights=[15,10,5,1])
array([ 3.70967742,  4.70967742,  5.70967742,  6.70967742,  7.70967742])
```

- np.min(a) max(a) 最大、最小值
- np.argmin(a) argmax(a) 最大、最小值降为一维后的下标
- np.unravel\_index(index, shape) 数组形状变为shape后 index 对应的下标
- np.ptp(a) 计算数组a中元素最大值与最小值的差
- np.median(a) 计算a中元素的中位数( 中值 )

## 梯度函数
#### np.gradient(f) 
- 计算数组 f 中元素的梯度, 当 f 为多维时, 返回每个维度梯度
- 梯度: 连续值之间的变化率, 即斜率
- 数组有多少维度, 函数的梯度就有多少维

```python
>>> a=np.arange(20).reshape(4,5)
>>> np.gradient(a)
[array([[ 5.,  5.,  5.,  5.,  5.],
       [ 5.,  5.,  5.,  5.,  5.],
       [ 5.,  5.,  5.,  5.,  5.],
       [ 5.,  5.,  5.,  5.,  5.]]), array([[ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.]])]
>>> a
array([[ 0,  1,  2,  3,  4],
       [ 5,  6,  7,  8,  9],
       [10, 11, 12, 13, 14],
       [15, 16, 17, 18, 19]])
# 三维
>>> c=np.random.randint(0,50,(3,2,3))
>>> c
array([[[30, 47, 20],
        [ 8, 27, 18]],

       [[ 6, 38, 21],
        [32, 43, 27]],

       [[20, 16,  6],
        [ 8, 42, 28]]])
>>> np.gradient(c)
[array([[[-24. ,  -9. ,   1. ],
        [ 24. ,  16. ,   9. ]],

       [[ -5. , -15.5,  -7. ],
        [  0. ,   7.5,   5. ]],

       [[ 14. , -22. , -15. ],
        [-24. ,  -1. ,   1. ]]]), array([[[-22., -20.,  -2.],
        [-22., -20.,  -2.]],

       [[ 26.,   5.,   6.],
        [ 26.,   5.,   6.]],

       [[-12.,  26.,  22.],
        [-12.,  26.,  22.]]]), array([[[ 17. ,  -5. , -27. ],
        [ 19. ,   5. ,  -9. ]],

       [[ 32. ,   7.5, -17. ],
        [ 11. ,  -2.5, -16. ]],

       [[ -4. ,  -7. , -10. ],
        [ 34. ,  10. , -14. ]]])]
```
