# NumPy - Python的科学计算库
- ndarry - n维数组
- 广播功能函数
- 整合c/c++/fortran代码的工具
- 线性代数、傅里叶变换、随机数等
- 是 SciPy、Pandas的基础
- 底层由C实现
- 多种长度的int、float、complex

## N维数组：ndarray
- 一般要求数组元素类型相同

```python	
import numpy as np
>>> a=np.array([[1,2,3],[1,2,4]])
>>> a
array([[1, 2, 3],
		[1, 2, 4]])
>>> a.ndim #轴的数量，或维度的数量
2
>>> a.size #对象中元素的个数
6
>>> a.shape #对象的尺度，对于矩阵就是n行m列
(2, 3)
>>> a.dtype #对象元素的类型
dtype('int64')
>>> a.itemsize #对象中每个元素的大小，以字节为单位
8
>>> b=np.array([[0,1],[4,5,6]])
>>> b.shape #b为非同质的array对象
(2,)
>>> b.dtype
dtype('O') #所以每一个元素都是一个‘O’，此时无法发挥出numpy的优势，所以应该避免
>>> b
array([list([0, 1]), list([4, 5, 6])], dtype=object)
```

#### 从Python的列表、元组等类型创建array
>x=np.array(list/tuple, dtype=np.float32)  
>dtype可指定也可不指定，由其推断

```python
>>> x=np.array([[0,1,2],[3,4,5],(4.1,4.3,-5)])
>>> x
array([[ 0. ,  1. ,  2. ],
       [ 3. ,  4. ,  5. ],
       [ 4.1,  4.3, -5. ]])

```
#### 使用NumPy中函数创建数组
>np.arange(n) 类似range()函数,支持3个参数的,提供了起点,终点和步长  
>np.ones(shape) 根据一个元组类型的shape返回一个全1的数组  
>np.zeros(shape)  
>np.full(shape,val) 生成的值全为val  
>np.eye(n) 创建正方n×n矩阵，对角线为1，其余均为0
>np.ones_like(a)  用 a 的shape
>np.zeros_like(a)  
>np.full_like(a,val)  

```python
#不指定的话都默认是浮点数
>>> np.ones((3,6))
array([[ 1.,  1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.,  1.]])
>>> np.zeros((3,6), dtype=np.int32)
array([[0, 0, 0, 0, 0, 0],
       [0, 0, 0, 0, 0, 0],
       [0, 0, 0, 0, 0, 0]], dtype=int32)
>>> np.eye(3)
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
>>> np.ones((2,3,4))
array([[[ 1.,  1.,  1.,  1.],
        [ 1.,  1.,  1.,  1.],
        [ 1.,  1.,  1.,  1.]],

       [[ 1.,  1.,  1.,  1.],
        [ 1.,  1.,  1.,  1.],
        [ 1.,  1.,  1.,  1.]]])
>>> np.full_like(a,4)
array([[ 4.,  4.,  4.],
       [ 4.,  4.,  4.],
       [ 4.,  4.,  4.]])
```
>np.linspace()  
>np.concatenate()

```python
#都是浮点数
>>> a=np.linspace(1,11,4)
>>> b=np.linspace(1,11,4,endpoint=False) #分成5份，不要最后一个
>>> c=np.concatenate((a,b)) #要求a和b个数一样
>>> print(a)
[  1.           4.33333333   7.66666667  11.        ]
>>> print(b)
[ 1.   3.5  6.   8.5]
>>> print(c)
[  1.           4.33333333   7.66666667  11.           1.           3.5
   6.           8.5       ]
>>> d=np.full((1,2),4)
>>> e=np.concatenate((a,b)) #a与b个数不一样
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: all the input arrays must have same number of dimensions
>>> f=np.full((2,2),3)
>>> g=np.concatenate((d,f))
>>> print(g)
[[4 4]
 [3 3]
 [3 3]]
```
>reshape(shape) 不改变元素，返回新数组 非原位  
>resize(shape) 与reshape类似，但是是原位操作  
>swapaxes(ax1,ax2) 将两个维度进行调换  
>flatten() 数组降为一维，返回新数组，非原位

```python
>>> a=np.full((4,5),5)
>>> a.reshape((4,4)) #要求与原来个数一样
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: cannot reshape array of size 20 into shape (4,4)
>>> a.reshape((2,10)) #返回了新的数组，非原位
array([[5, 5, 5, 5, 5, 5, 5, 5, 5, 5],
       [5, 5, 5, 5, 5, 5, 5, 5, 5, 5]])
>>> a
array([[5, 5, 5, 5, 5],
       [5, 5, 5, 5, 5],
       [5, 5, 5, 5, 5],
       [5, 5, 5, 5, 5]])
>>> a.resize((2,2)) #要求与原来个数一样
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: cannot resize an array that references or is referenced
by another array in this way.  Use the resize function
>>> a.resize((2,10)) #未返回值，原位操作
>>> a
array([[5, 5, 5, 5, 5, 5, 5, 5, 5, 5],
       [5, 5, 5, 5, 5, 5, 5, 5, 5, 5]])
>>> a.flatten() #返回了新的值，非原位
array([5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5])
```
>astype(new_type) 转换元素类型，返回新值，非原位  
>tolist() 返回列表，列表的计算比 numpy 的计算慢的多

```python
>>> a=np.full((2,3,1),2,dtype=np.int) #np.int 未指定32或64，由程序自动选择
>>> b=a.astype(np.float) #np.float 未指定32或64，由程序自动选择
>>> a
array([[[2],
        [2],
        [2]],

       [[2],
        [2],
        [2]]])
>>> b
array([[[ 2.],
        [ 2.],
        [ 2.]],

       [[ 2.],
        [ 2.],
        [ 2.]]])
>>> a.tolist()
[[[2], [2], [2]], [[2], [2], [2]]]
```
#### 类似 Python 的切片
- Python 内建的 numpy 同样支持
```python
>>> a=np.linspace(1,11,3)
>>> a[2]
11.0
>>> a
array([  1.,   6.,  11.])
>>> a[::1]
array([  1.,   6.,  11.])
>>> a[::2]
array([  1.,  11.])
```
- 多维数组的切片
```python
>>> a=np.arange(24).reshape((2,3,4))
>>> a[:,:,:] #可以对各个维度切片，本行与直接输出 a 结果相同
array([[[ 0,  1,  2,  3],
        [ 4,  5,  6,  7],
        [ 8,  9, 10, 11]],

       [[12, 13, 14, 15],
        [16, 17, 18, 19],
        [20, 21, 22, 23]]])
>>> a[0,0,1]
1
>>> a[1,1,1]
17
>>> a[-1,-1,-1]
23
# 切片
>>> a[0:2,0,0]
array([ 0, 12])
>>> a[1:,1:,1:]
array([[[17, 18, 19],
        [21, 22, 23]]])
>>> a[::1,::2,::2]
array([[[ 0,  2],
        [ 8, 10]],

       [[12, 14],
        [20, 22]]])
```
#### 与标量的计算
```python
>>> a=np.arange(24).reshape(2,3,4)
>>> a.mean()
11.5
>>> a=a/a.mean()
>>> a
array([[[ 0.        ,  0.08695652,  0.17391304,  0.26086957],
        [ 0.34782609,  0.43478261,  0.52173913,  0.60869565],
        [ 0.69565217,  0.7826087 ,  0.86956522,  0.95652174]],
jueduizhi
       [[ 1.04347826,  1.13043478,  1.2173913 ,  1.30434783],
        [ 1.39130435,  1.47826087,  1.56521739,  1.65217391],
        [ 1.73913043,  1.82608696,  1.91304348,  2.        ]]])
```
#### 一元函数
>np.abs(x), np.fabs() - 计算各个元素的绝对值,fabs()为浮点数的  
>np.sqrt(x) - 计算各个元素的平方根  
>np.square(x) - 计算各个元素的平方  
>np.log(x), np.log10(x), np.log2(x) 计算各个元素的自然对数、10底对数、2底对数  
>np.ceil(x), np.floor(x) 计算各个元素大于其的最小整数、小于其的最大整数  
>np.rint(x) 计算各个元素四舍五入的值  
>np.modf(x) 将各元素小数部分和整数部分以两个独立数组形式返回，可 `b,c=np.modf(a)`，b保存小数部分，c保存整数部分  
>np.cos(x), np.sin(x), np.tan(x), np.cosh(x), np.sinh(x), np.tanh(x) 计算各元素的普通型三角函数和双曲型三角函数  
>np.exp(x) 计算各元素的指数值  
>np.sign(x) 计算各元素的符号值，1(+), 0, -1(-)  
>............大量函数

```python
>>> a=np.full((2,3),-5.1)
>>> np.fabs(a)
array([[ 5.1,  5.1,  5.1],
       [ 5.1,  5.1,  5.1]])
>>> np.ceil(a)
array([[-5., -5., -5.],
       [-5., -5., -5.]])
>>> np.floor(a)
array([[-6., -6., -6.],
       [-6., -6., -6.]])
>>> b,c=np.modf(a)
>>> b
array([[-0.1, -0.1, -0.1],
       [-0.1, -0.1, -0.1]])
>>> c
array([[-5., -5., -5.],
       [-5., -5., -5.]])
>>> np.exp(a)
array([[ 0.00609675,  0.00609675,  0.00609675],
       [ 0.00609675,  0.00609675,  0.00609675]])
```
#### 二元函数
> \+ \- \* / 两个数组各元素分别进行对应计算  
> np.maximum(x,y), np.minimum(x,y), np.fmax(), np.fmin() 各元素的大值、小值，fmax与fmin将忽略 NaN  
> np.mod(x,y) 元素级的模运算 ， x为被除数，y为除数  
> np,copysign(x,y) 将y中各元素的符号值赋给x  
> \>= <= == != 元素级的比较，产生bool数组  

```python
>>> a=np.full((1,6),4)
>>> b=np.square(np.arange(6))
>>> np.mod(a,b)
array([[0, 0, 0, 4, 4, 4]])
>>> a
array([[4, 4, 4, 4, 4, 4]])
>>> b
array([ 0,  1,  4,  9, 16, 25]) #除数里有0，好像没有影响
>>> a+=1
>>> a>b
array([[ True,  True,  True, False, False, False]], dtype=bool)
```
