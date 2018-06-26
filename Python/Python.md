# Beginning Python From Novice to Professional
## 第一章
- `repr` 把结果字符串转换为合法的Python表达式
```python
>>> def lengthMsg(x):
...     print('The length of', repr(x), 'is', len(x))
... 
>>> lengthMsg([1,2,3])
The length of [1, 2, 3] is 3
>>> lengthMsg('Fond')
The length of 'Fond' is 4       
```

- `str` 把值转换为合理形式的字符串

```python
>>> def lengthMsg2(x):
...     print('The length2 of', str(x), 'is', len(x))
... 
>>> lengthMsg2([1,2,3])
The length2 of [1, 2, 3] is 3
>>> lengthMsg2('Fond')
The length2 of Fond is 4
```

二者区别并不是很明显，repr转为了表达式，但str只是字符串
## 第二章 列表和元组
- 切片

```python
>>> numbers = [0,1,2,3,4,5,6,7,8,9]
>>> numbers[3:6]
[3,4,5]
 >>> numbers[-3:]
 [7,8,9]
 >>> numbers[0:10:2]
[0,2,4,6,8]
>>> numbers[::3]
[0,3,6,9]
>>> numbers[1:1] = [3,4,5]
>>> numbers
[0,3,4,5,1,2,3,4,5,6,7,8,9]
```

- 整除运算符`//`

```
>>> 5/3
1.666666666667
>>> 5//3
1
```

- 列表`list`的方法
	- `append`
```
>>> ['to','to','be'].append('not')
'to','to','be','not']
```
	- `count`
```
>>> ['to','to','be'].count('to')
2
```
	- `extend`
```
>>> a = [1,2,3]
>>> b = [4,5,6]
>>> a.extend(b)
>>> a
[1,2,3,4,5,6]
```
	- `index`
```
>>> a=[1,2,3,4]
>>> a.index(4)
3
```
	- `insert`
	- `pop`	 **删除并返回，唯一一个既修改列表又返回元素值（除了None）**
	- `remove`
	- `reverse`
	- `sort`
	- 高级排序 `sort`函数接受一个`key`，可以指定排序的依据；还有 *bool* 型`reverse`决定是否反转
```
>>> a = [[1,100,10],[2,20,200],[30,3,300],[400,40,4]]
>>> a
[[1,100,10],[2,20,200],[30,3,300],[400,40,4]]
>>> a.sort(key = lambda x : x[1])
>>> a
[[30,3,300],[2,20,200],[400,40,4],[1,100,10]]
```

## 第三章 使用字符串
- 格式化字符串
	1. %字符
	2. 转换标志（可选） "-"表示左对齐；"+"表示转换值前加正负号；" "空格表示正数之前留空；"0"表示若位数不够用0填充
	3. 最小字符宽度（可选） 用"\*"则表示从宽度从值中读取
	4. 点（.）后跟精度值（可选） 用"\*"表示从值中读取
	5. 转换类型
- 字符串`str`的函数
	- `find`
	- **`join`**
```	
>>> seq = ['1','2','3','4','5']
>>> sep = '+'
>>> sep.join(seq)
'1+2+3+4+5'
```
	- `lower` 返回字符串的小写字母版
	- `replace`  返回某字符串的所有匹配项均被替换后的字符串
```
>>> 'You are a good boy'.replace('o','FFF')
'YFFFu are a gFFFFFFd bFFFy'            
```            
	- **`split`** 是`join`的逆方法
	- `strip` 去除字符串两侧的空格
	- `translate` 与linux中的`tr`类似
```	
>>> table = str.maketrans('cs','kz')
>>> oneString = 'They are cool students'.translate(table)
>>> oneString
'They are kool ztudentz'
```

## 第四章 字典
- 字典（`dict`）的格式化字符串
```
>>>phoneBook = {'zs':'1000','ls':'2000','ww':'3000','zl':'4000'}
>>> "ls's phone is %(ls)s." %phoneBook
"zs's phone is 2000."
```        
	在用于模板系统时非常有用
```
>>> template = "%(name)s is a good %(career)s."
>>> table = {'name' : 'zhangsan', 'career' : 'students'}
>>> print (template % table)
zhangsan is a good students.
```        


- 字典方法
	- `clear` 原位操作
	- `copy` 这是**浅复制**，但**比直接赋值要深**，直接赋值只是相当于别名
``` 
>>> dictA = {'name' : 'zs', 'score' : [84,90,32] }
>>> dictB = dictA.copy()
>>> dictB['score'].remove(84)
>>> dictA
{'name': 'zs', 'score': [90, 32]}
>>> dictB
{'name': 'zs', 'score': [90, 32]}
```        
		可以使用`copy`模块的`deepcopy()`实现深复制
``` 
>>> from copy import deepcopy
>>> dictC = deepcopy(dictA)
>>> dictC['score'].remove(90)
>>> dictC
{'name': 'zs', 'score': [32]}
>>> dictA
{'name': 'zs', 'score': [90, 32]}
```        
	- `fromkeys` 根据给定的值建立字典
```
#'unknown'是可选的，不提供则默认值为'None'
>>> dictD = dict.fromkeys(['name', 'age'], 'unknown')
>>> dictD
{'name': 'unknown', 'age': 'unknown'}
```
	- `get` 访问字典中不存在的键时不会出错
```
>>> print(dictD['career'])
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
KeyError: 'career'
#'unknown'是可选的，不提供则为默认值'None'
>>> print(dictD.get('career'， 'unknown') )
unknown
```
	- `items`
```
>>> dictD = dict.fromkeys(['name', 'age', 'career'], 'default')
>>> dictD.items()
dict_items([('name', 'default'), ('age', 'default'), ('career', 'default')])
```
    - `keys`
```
>>> dictD.keys()
dict_keys(['name', 'age', 'career'])
```
    - `values`
```
>>> dictD.values()
dict_values(['default', 'default', 'default'])
```
	- `pop` 可以看到既删除元素又返回删除的元素
```
>>> dictD.pop('name')
'default'
>>> dictD
{'age': 'default', 'career': 'default'}
```
    - `popitem` 弹出随机元素，因为字典并没有顺序这一说
```
>>> dictD.popitem()
('career', 'default')
>>> dictD.popitem()
('age', 'default')
```     
	- `setdefault`
```
>>> dictE ={}
>>> dictE.setdefault('name', 'noName')
'noName'
>>> dictE
{'name': 'noName'}
>>> dictE['age'] = 23
>>> dictE.setdefault('age', 0)
23
```
	- `update`
```
>>> dictE = {'name': 'noName', 'age': 23}
>>> dictF = {'name' : 'zs', 'career' : 'student'}
>>> dictF.update(dictE)
>>> dictF
{'name': 'noName', 'career': 'student', 'age': 23}
```


## 第五章 条件、循环和其他语句
- `print`函数
```
>>> print('age', 42)
age 42
>>> print('age' '42')
age42
```
- 序列解包
```
>>> values = [1,2,3]
>>> x,y,z = values
>>> x
1
>>> valuesTuple = (4,5,6)
>>> a,b,c = valuesTuple
>>> a
4
>>> dictG = {'name' : 'lisi', 'age' : 26}
>>> key, value = dictG.popitem()
>>> key
'age'
>>> value
26
```
- `is` 一个特殊比较符
```
>>> x = [1,2]
>>> y = [1,2]
>>> x == y
True
>>> x is y
False
>>> d = x
>>> d is x
True
```
	避免将`is`用于比较类似数值和字符串这类不可变值，因为python内部实现的原因，使用is的结果是不可预测的。
- `zip` 并行迭代，可处理不等长的序列
```
>>> name = ['zs', 'ls', 'ww', 'zl']
>>> age = range(23,100,5)
>>> for a in zip(name,age):
...     print(a)
... 
('zs', 23)
('ls', 28)
('ww', 33)
('zl', 38)
```
- `enumerate` 按索引迭代
``` 
>>> strings = ['good', 'yes', 'ok']
>>> for index, string in enumerate(strings):
...     print(index, string)
... 
0 good
1 yes
2 ok
```

- 翻转和排序迭代，`sorted`返回列表，`reversed`返回迭代器
``` 
>>> a = [[1,100,10],[2,20,200],[30,3,300],[400,40,4]]
>>> e =  reversed ( sorted(a, key = lambda x : x[2]) ) 
>>> print (e)
<list_reverseiterator object at 0x7f78677b2518>
>>> for x in e:
...     print (x)
... 
[30, 3, 300]
[2, 20, 200]
[1, 100, 10]
[400, 40, 4]
```

- for循环的else子句， 用于没有调用`break`时执行
```
>>> def testBreak( x ):
...     for a in range (x):
...             if a==11:
...                     print("break")
...                     break;
...     else:
...             print("not break")
... 
>>> testBreak(11)
not break
>>> testBreak(12)
break
```

- `exec`与`eval`，很不安全，前者可能会失去对代码执行的控制，后者可能会把一些变量赋为其它值
```
#scope 为作用域
>>> a = 5
>>> scope = {}
>>> exec ('a=2', scope)
>>> eval ('a*a' ,scope)
4
>>> scope['a'] = 3
>>> exec ('print(a*a)' ,scope)
9
>>> a
5
```

## 第六章 抽象
- python语言固有的特性，如果函数传入的是不可变元素（数、字符串等），那么在函数内部改变参数的值的做法将无法影响到函数外面的值。可以用返回值，或者将值放在列表（list）中传入
- 收集参数
```
# *号意味这将参数收集起来，当做一个元组（Tuple）
>>> def storePara(*params):
...     print(params)
... 
>>> storePara(1,2,3,4,5)
(1, 2, 3, 4, 5)
>>> storePara()
()
#**两个星号表示收集元素作为字典（Dict）
>>> def storePara2(x,y, *para, **paraMap):
...     print (x)
...     print (y)
...     print (para)
...     print (paraMap)
... 
>>> storePara2(1,2,3,4,5,6,76,key=1,value=3,age=4)
1
2
(3, 4, 5, 6, 7)
{'key': 1, 'value': 3, 'age': 4}
```

- 在定义或调用函数时使用的\*号或双星号表示仅传递字典或元组
- 函数的嵌套
```
>>> def divider(factor):
...     def divide(number):
...             return number/factor
...     return divide
... 
>>> g = divider(2)
>>> g(6)
3.0
>>> g(9)
4.5
>>> divider(3)(13)
4.333333333333333
```
嵌套有时会产生意外，因为没有正确的闭包
```
>>> def count():
...     fs = []
...     for i in range(1,4):
...             def f():
...                     return i*i
...             fs.append(f)
...     return fs
... 
>>> f1,f2,f3 = count() #因为返回的三个函数都未绑定参数
>>> f1()
9
>>> f2()
9
>>> f3()
9
```
正确示例1，返回了值
```
>>> def count2():
...     fs = []
...     def f(j): #绑定了参数
...             return j*j
...     for i in range(1,4):
...             fs.append(f(i))
...     return fs
... 
>>> f1,f2,f3 = count2() #此时返回的实际上是三个值，而不是函数
>>> f1()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
>>> f1
1
>>> f2
4
>>> f3
9
```
正确示例2，返回函数
```
>>> def count3():
...     def f(j):
...             def g():
...                     return j*j
...             return g
...     fs = []
...     for i in range(1,4):
...             fs.append(f(i))
...     return fs
... 
>>> f1,f2,f3 = count3()
>>> f1 #现在是一个函数
<function count3.<locals>.f.<locals>.g at 0x7f74e12eb950>
>>> f1()
1
>>> f2()
4
>>> f3()
9
```

- `sum(seq)`
```        
>>> a=[1,2,3]
>>> sum(a)
6
```

- `map(func, seq)`	返回迭代器，为seq中每个元素调用func
```
>>> a=[1,2,3]
>>> def out(s):
...     print(s)
...
>>> f = map(out,a)
>>> next(f)
1
>>> next(f)
2
>>> next(f)
3
```        


- `reduce(func, seq)` 将结果的和作为元素与下一个元素一起调用func

```
>>> a=[1,2,3]
>>> def add(x,y):
...     return x+y
... 
>>> from functools import reduce
>>> reduce(add, a)
6
```
 
- `filter(func, seq)` 对每个元素调用func，返回一个迭代器，产生调用func结果为真的元素

```
>>> a = (1,2,3,4,5,6,7,8)
>>> def biger(a):
...     if a> 5:
...             return True
...     return False
... 
>>> f = filter(biger,a)
>>> next(f)
6
>>> next(f)
7
```

## 第七章 更加抽象
1. random模块的`choice`可从列表中随机选出元素
2. `issubclass(class1, class2)` 检查class1是否为class2的子类
3. 多重继承时的问题，`class TalkingCalculator(Talker, Caltulator): pass`第一个继承的类中的方法会覆盖第二个类中的同名方法，这是MRO（Method Resolution Order，方法判定顺序）
4. `callable(item)`，在python3.0中不可用，3.2以后又添加了

## 第八章 异常
- 捕获异常的地方调用不带参数的 `raise` 表示继续传递捕获到的异常
- break 与异常结合，只有在没有发生异常的情况下才break
- finally，用来做最后的清理，无论如何都会被调用
```
while True:
    try:
        x = input()
        y = input()
        print( x/y  )
    except:
        print( "Wrong Value. Please try again." )
    else:
        break
    finally:
        print( "Cleaning.."  )
```

- 在很多情况下，使用 trt/except 比使用 if/else 更自然些。在做一件事情时去处理可能出现的错误，而不是在开始做事前就进行大量的检查。这个策略可以总结为 Leap Before You Look (看前就跳)


## 第九章 魔法方法、属性和迭代器
- 类或类型检查和Python中多态的目标背道而驰
- 基本的序列和映射规则:
    -  `__len__`(self): 返回集合中元素数量
    - `__getitem__(self, key)`: 返回与键对应的值
    - `__setitem__(self, key, value)`: 存储和key相关的value
    - `__delitem__(self, key)`: 删除key对应的值 

- 可以子类化内建类型，如果类的行为和默认行为很接近时很有用，不然还不如重写一个类
- `property`函数，如果该函数行为奇怪，请确保你所使用的类为新式类
```
class Rectangle:
    def __init__(self):
        self.width = 0
        self.height = 0
    def setSize(self, size):
        self.width, self.height = size
    def getSize(self):
        return self.width, self.height
    size = property(getSize, setSize)
>>> r = Rectangle()
>>> r.width = 10
>>> r.height = 5
>>> r.size
(10, 5)
>>> r.size = 150， 100
>>> r.width
150
```


### `__getattr__`、`__setattr__`:
- `__getattribute__(self, name)`: 当特性name被访问时自动被调用（只有新式类能用）
- `__getattr__(self, name)`: 当特性name被访问且对象没有相应的特性时被自动调用
- `__setattr__(self, name, value)`: 当试图给name特性赋值时自动调用
- `__delattr__(self, name)`: 当试图删除特性name时自动调用
```
class Rectangle:
    def __init__(self):
        self.width = 0
        self.height = 0
    def __setattr__(self, name, value):
        if name == 'size':
            self.width, self.height = value
        else:
            self.__dict__[name] = value
    def __getattr__(sekf, name):
        if name == `size`:
            return self.width, self.height
        else:
            raise AttributeError
```

- 该版本需要管理其它细节  
    - `__setattr__`方法在涉及的特性不是size时也会被调用；如果是size，就执行赋值操作，否则就要使用特殊方法:`__dict__`，该特殊方法包含一个字典，字典里是所有实例的属性。为了避免`__setattr__`被再次调用，从而陷入无限循环，`__dict__`被用来代替普通特性赋值
    - `__getattr__`只在普通特性没有找到时调用。如果希望类和 hasattr 和 getattr 这样的内建函数一起工作，那么`__getattr__`就很重要
    - `__getattribute__`会拦截所有特性的访问，包括`__dict__`的访问！所以访问`__getattribute__`中与 self 相关的特性时，使用超类的`__getattribute__`方法（使用super函数）是唯一安全的途径


### 迭代器: `__iter__`
- 需要`__iter__`和`__next__`方法
```
class Fibs:
    def __init__(self):
        self.a = 0
        self.b = 1
    def __next__(self):
        self.a, self.b = self.b, self.a+self.b
    def __iter__(self):
        return self
```


- 一个实现了`__iter__`方法的对象是可迭代的，一个实现了`__next__`方法的对象则是迭代器
```
fibs = Fibs()
for f in fibs:
    if f > 1000:
        print(f)
        break;
```
因为实现了`__iter__`方法，返回一个迭代器，所以它是可迭代的


- 可以使用内建的 `iter` 函数从可迭代的对象中获得迭代器
- 可以使用list构造函数显示地把迭代器直接转换为序列

### 生成器: `yield`
- 任何包含 `yield` 语句的函数称为**生成器**。它们不是像 return 那样返回值，而是每次产生值以后，都冻结在那里，等待再次唤醒
- 生成器推导式与列表推导式的区别: 括号不同
```
>>> g = ( i**2 for i in range(2,6) )
>>> next(g)
4
>>> f = [ i**2 for i in range(2,6) ]
>>> f
[4, 9, 16, 25]
```


- 递归生成器: 可嵌套任意层循环
```
def flatten(nested):
    try:
        for sublist in nested:
            for element in flatten(sublist):
                yield element
    except TypeError:
        yield nested
```
但处理字符串时会有问题： 1.字符串不会引发TypeError；2.字符串的第一个字符实际上是另一个长度为1的字符串，会引发无穷递归  
一个解决方法:
```
def flatten(nested):
    try:
        #不迭代类似字符串的对象
        try: nested + ''
        except TypeError: pass
        else: raise TypeError
        for sublist in nested:
            for element in flatten(sublist):
                yield element
        except TypeError:
            yield nested
>>> list( flatten(['foo', ['bar', ['baz']]]) )
['foo, 'bar', 'baz']
```

- 八皇后问题
https://github.com/zj0395/myStudy/blob/master/myPython/queens.py


## 第十章 自带电池
- pprint模块的pprint函数能提供更智能的输出
- `__init__.py`用于把文件夹视作包（package)
- `__file__` 变量: 可以获得某个模块的路径
- `__all__` 变量，定义了模块的公有接口，import \* 时不会导入这个变量以外的值
```
>>> import copy
>>> dir(copy)
['Error', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', '_copy_dispatch', '_copy_immutable', '_deepcopy_atomic', '_deepcopy_dict', '_deepcopy_dispatch', '_deepcopy_list', '_deepcopy_method', '_deepcopy_tuple', '_keep_alive', '_reconstruct', 'copy', 'deepcopy', 'dispatch_table', 'error']
>>> copy.__all__
['Error', 'copy', 'deepcopy']
>>>
```

- 用 help 获取帮助
```
>>> help(copy.copy)
```


### sys 模块
- 一些重要函数和变量

|函数/变量|描述|
|:--:|:--:|
|argv|命令行调用传递的参数，包括脚本的名称
|exit(\[arg])|退出当前程序，可选返回值
|modules|映射模块名字到载入模块的字典
|path|查找模块所在目录的目录名列表
|platform|平台的标识符，类似win32或linux
|stdin|标准输入流——一个类文件（file-like）对象
|stdout|标准输出流——一个类文件对象
|stderr|标准错误流——一个类文件对象


### os 模块
- 一些重要函数和变量

|函数/变量|描述
|:--:|:--:|
|environ|对环境变量进行映射
|system(command)|在子shell中执行系统命令
|sep|路径中的分隔符
|pathsep|分割路径的分隔符
|linesep|行分隔符（'\n', '\r', '\r\n'）
|urandom(n)|返回n字节的加密强随机数据


- 此外，os.path还包括一些用于检查、构造、删除目录和文件的函数，以及一些处理路径的函数，例如 os.path.split 和 os.path.join 让你在大部分情况下都可以忽略 os.pathsep  
大多数情况下，os.system 函数很有用，但启动浏览器这样的特定任务可以交给其它模块，如 webbrowser 模块

### fileinput
fileinput.input (files=None, inplace=False, backup='', bufsize=0, mode='r', openhook=None)

- 一些重要函数和变量

|函数/变量|描述|
|:--:|:-:|
|input()|用于遍历多个输入流中的行
|filename()|返回当前文件名字
|lineno()|返回当前累计的行数
|filelineno()|返回当前文件的行数
|isfirstline()|检查当前行是否是第一行
|isstdin()|检查最后一行是否来自stdin
|nextfile()|关闭当前文件，移动到下一个文件
|close()|关闭序列


### set、heap 和 deque
- set，是可变的，但只能包含不可变（可散列的）元素，所以不能包含其它集合
- frozenset 用于代表不可变的集合
- heap，优先队列的一种。python并没有独立的堆类型，只有一个包含一些堆操作函数的模块`heapq`
- deque 能够有效地旋转（rotate）元素，使头尾相连


### time
- 一些重要函数

|函数|描述|
|:--:|:-:|
|asctime(\[tuple])|将时间元祖转换为字符串
|localtime(\[sece])|将秒数转换为日期元祖，以本地时间为准
|mktime(tuple)|将时间元祖转换为本地时间
|sleep(secs)|休眠secs秒
|strptime(string, format)|将字符串解析为时间元祖
|time()|当前时间，秒数


- python日期元组的字段含义

|索 引|字 段|值
|:-:|:-:|:--:|
|0|年|比如2007
|1|月|范围1-12
|2|日|范围1-31
|3|时|范围0-23
|4|分|范围0-59
|5|秒|范围0-61（为了应付闰秒和双闰秒）
|6|周|当周一为0时，范围为0-6
|7|儒历日|范围1-366
|8|夏令时|0、1或-1，为-1时，mktime会工作正常


### random
- 一些重要函数

|函数|描述
|:--:|:-:|
|random()|返回0-1之间的随机实数，不包括0，包括1
|getrandbits(n)|以长整数形式返回n个随机位
|uniform(a, b)|返回a-b间的随机实数，包括a，不包括b
|randrange(start, stop, setp)|返回随机数
|choice(seq)|返回序列seq中的任意元素
|shuffle(seq, random)|原地指定序列seq
|sample(seq, n)|从序列seq中选择n个随机且独立的元素


### shelve
- 简单的存储方案，可当做普通的字典使用，但键一定是字符串
- 潜在的陷阱
```
>>> import shelve
>>> s = shelve.open('test.dat')
>>> s['x'] = ['a', 'sa',' qw']
>>> s['x'].append('af')
>>> s['x']
['a', 'sa', ' qw']
```
即直接对`s['x]']`调用append是生成了一个副本，正确用法是先绑定到临时变量上，赋值后在赋值回来；也可以在open的时候把 writeback 参数设为 True
```
>>> temp = s['x']
>>> temp.append('e')
>>> s['x'] = temp
>>> s['x']
['a', 'sa', ' qw', 'e']
```


### re
- 一些重要函数

|函数|描述|
|:--:|:-:|
|compile(pattern, flag)|根据正则表达式的字符串模式创建对象，使用对象效率更高
|search(pattern, string, flag)|在整个字符串中查找
|match(pattern, string, flag)|只在字符串的开头查找
|split(pattern, string, maxsplit=0)|根据模式的匹配项来分割字符串
|findall(pattern, string)|列出所有匹配项
|sub(pat, repl, stirng, count=0)|将字符串中匹配pat的替换为repl
escape(string)|将字符串中所有特殊正则表达式字符转义，根据字符串生成匹配该字符串的表达式

- group
```
>>> re.match('p','python').group(0)
'p'
```
每个()包起来的都为1个组，整个匹配结果为group(0)  
根据 group 号可以进行灵活的查找替换


- 贪婪模式和非贪婪模式   
正则表达式默认是贪婪的，即进行尽可能多的匹配  
```
#贪婪的
>>> pattern = r'\*\*(.+)\*\*'
>>> re.sub(pattern, r'<em>\1</em>', '**This** is **it**!')
'<em>This** is **it</em>!'
#非贪婪的
>>> pattern = r'\*\*(.+?)\*\*'
>>> re.sub(pattern, r'<em>\1</em>', '**This** is **it**!')
'<em>This</em> is <em>it</em>!'
```
使用 `+?` 代替 `+` 意味着它会进行尽可能少的匹配，在到达`\*\*`的下一个匹配项之前匹配最少的内容。并把pattern的 group1 替换到占位符`\1`那里

- 使用正则表达式的模板  
https://github.com/zj0395/myStudy/blob/master/myPython/templatesRE.py


### 其它模块
- functools: 通过部分参数来使用某个函数（部分求值），稍后再为剩下的参数提供数值
- difflib: 计算两个序列的相似程度
- hashlib: 计算签名，用于加密和安全性
- md5 和 sha 模块，用于安全性
- csv: 读取CSV文件，逗号分隔值(Comma-Separated Values)。以显而易见的方式来处理这种格式的某些很难处理的地方
- timeit、profile 和 trace: 模块 timeit 衡量代码运行时间，应该用它来代替 time 模块进行测试；profile进行效率分析；trace进行那哪些部分执行了，哪些部分没执行的分析:w
- datetime: 如果 time 模块不能满足时间追踪方面的需求，那么 datetime 可有用武之地。接口比 time 模块更直观
- itertools: 有很多工具用来创建和联合迭代器，还包括以下函数：将可迭代对象链接起来、创建返回无限连续整数的迭代器（和range类似但没有上限）
- logging: 提供了一组标准工具以便让开发人员管理一个或多个核心日志文件，同时还提供了多组优先级
- getopt 和 optprase: 解析运行时的选项，-r -a 等。getopt库是个切实可行的解决方案，而optprase则更新、更强大并且更易用
- cmd: 可以编写命令行解释器，就像python解释器一样，可以自定义命令；也许还能把它作为程序的用户界面


## 第十一章 文件和流
- read 和 write
- readlines 和 writelines
- readline，没有 writeline
- 关闭文件，一种防止忘记 close的方法:
```
with open("file.txt") as file:
    doSomething(file)
```


### 迭代文件内容
- 按字节处理，频繁的使用break可能会让代码很难懂

```
f = open( fileName )
while True:
    char = f.read(1)
    if not char: break
    process( char )
f.close()
```


- 按行操作

```
f = open( fileName )
while True:
    line = f.readline()
    if not line: break
    process(line)
f.close()
```


- 文件不是很大时，就用 read 或 readlines 读取所有内容，然后分别用 char 和 line 进行迭代

### 懒惰行迭代
- 它只读实际需要的文件部分

```
import fileinput
for line in fileinput.input(filename):
    process(line)
```


### 文件迭代器
文件对象是可迭代的  
```
f = open( fileName )
for line in f:
    process( line )
f.close()
```

不用变量存储文件对象，无需关闭文件
```
for　line in open(fileName):
    process(line)
```

sys.stdin也是可迭代的
```
import sys
for line in sys.stdin:
    process(line)
```


## 第十二章 图形用户界面

## 第十三章 数据库支持

## 第十四章 网络编程
### socket 模块
- 三个参数，地址族（默认socket.AF\_INET)、流（socket.SOCK\_STREAM）或数据报文（socket.SOCK\_DGRAM）、使用的协议（默认为0）
- 服务器端使用 bind 后需要 listen 去监听，然后 accept 阻塞等待客户端连接
- 客户端直接使用 connect

### urllib 和 urllib2 模块
- 与 re 模块结合很强大
- urlopen 返回的类文件对象支持 close、read、readline 和 readlines，也支持迭代
- urlretrieve 下载文件并保存为本地副本

### SocketServer 模块
### 带有 select 和 poll 的异步 I/O
- 一个使用了 select 的简单服务器
```
import socket, select
s = socket.socket()
host = socket.gethostname()
port = 1234
s.bind( host, port )

s.listen( 5 )
inputs = [s]
while True:
    rs, ws, es = select.select( inputs, [], [] )
    for r in rs:
        if r is s:
            c, addr = s.accept()
            print('Got connection from', addr)
            inputs.append( c )
        else:
            try:
                data = r.recv(1024)
                disconnected = not data
            except socket.error:
                disconnected = True

            if disconnected:
                print( r.getpeername(), 'disconnected' )
                inputs.remove( r )
            else:
                print( data )
```


## 第十五章 Python 和 Web
### Tidy 和 XHTML 解析
- Tidy 是用来修复不规范且有些随意的HTML文档的工具，它会确保文档的格式是正确的
```
from subprocess import Popen, PIPE

text = open("messy.html", 'rb').read()
tidy = Popen('tidy', stdin=PIPE, stdout=PIPE, stderr=PIPE)

tidy.stdin.write(text)
tidy.stdin.close()

print( tidy.stdout.read().decode('utf-8') )
```


- BeautifulSoup  
https://github.com/zj0395/myStudy/blob/master/myPython/function.py


## 第十六章 测试
## 第十七章 扩展Python
## 第十八章 程序打包
## 第十九章 好玩的编程
- 灵活性、原型设计、配置、日志记录
- ConfigParser 模块，解析 ini 配置文件  
https://github.com/zj0395/myStudy/blob/master/myPython/function.py

- logging 模块，进行日志记录
