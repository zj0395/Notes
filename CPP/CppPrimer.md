# C++ Primer
## 第一章 开始
- 在UNIX系统中,可以通过 `echo $?` 获得上一个执行命令的返回值
- 流 stream 想要表达的意思是,随着时间的推移,字符是顺序生成或消耗的
- cerr , clog
- clog, 一个 ostream 对象, 默认是被缓冲的; 用于日志
- 注释符不能嵌套,防止嵌套注释错误,最好还是使用单行注释注释掉每一行 
- 缓冲区: IO设施通常将输入(或输出)数据保存在一个缓冲区,默认情况下，读 cin 会刷新缓冲区;程序非正常终止也会刷新 cout

## 第二章 变量和基本类型
- 执行浮点数用 double, 因为 float 通常精度不够, 且二者计算代价相差无几。对于某些机器 double 比单精度更快。long double 提供的精度一般情况下是没有必要的，且带来的运行时消耗也不容忽视。
- 建议: 避免无法预知和依赖于实现环境的行为
- 提示: 切勿混用带符号类型和无符号类型
```
L'a';//宽字符
u8"hi!";//utf-8字符串常量
42ULL;//无符号整形字面量, unsigned long long
1E-3F;//单精度,float
3.14159L;//long double
```


- 任何包含了显示初始化的声明即成为定义
- C++为标准库保留了一些名字。用户不要连续出现两个下划线，也不要以下划线紧连大写字母开头
- C++关键字, 包括C++11

| 关键字 
| :----: | :--: |:-:|:-:|:-:
|alignas|continue|friend|register|true
|alignof|decltype|goto|reinterpret\_cast|try
|asm|default|if|return|typedef
|auto|delete|inline|short|typeid
|bool|do|int|signed|typename
|break|double|long|sizeof|union
|case|dynamic\_cast|mutable|static|unsigned
|catch|else|namespace|static\_assert|using
|char|enum|new|static\_cast|virtual
|char16\_t|explicit|noexcept|struct|void
|char32\_t|export|nullptr|switch|volatile
|class|extern|operator|template|wchar\_t
|constexpr|float|protected|thread\_local
|const\_cast|for|public|throw

- 以 void\* 视角来看, 内存空间也就仅仅是内存空间, 没办法访问其内部对象
- 指向指针的引用
```
int i=42;
int* p;
int* &r = p;
r = &i;
*r=0;//改变了i
```


- 使用 **constexpr** 以由编译器来验证变量是否是一个常量表达式
- 类型的别名声明 `using myInt = int`, 等价于`typedef int myInt`
- auto, 自动推断类型, 一般会忽略顶层 const
- decltype(expr) var;
>1. 如果 expr 是一个没有括号括起的标识符, 则 var 类型与该标识符类型相同, 包括顶层const  
>2. 如果 expr 是个函数调用, 则 var 类型与函数返回类型相同  
>3. 如果 expr 是个左值, 则 var 为指向其类型的引用。明显的情况就是 expr 是个括号括起的标识符。

- bind 令某个名字与给定的实体关联在一起，使用该名字就是使用该实体。例如，引用

## 第三章 字符串、向量和数组
- size() 函数返回的是 size\_type 类型，是无符号的；不要与带符号的一起用
```
string str("Nothing");
for( auto c : str)
	cout << c << endl;
decltype( str.size() ) count = 0;
```


- 基于范围的 for 循环不应改变其所遍历序列的大小；因为它预存了 str.end() 作为结束条件
- 确保下标合法的一种有效手段就是尽可能使用范围 for 语句
- 凡是使用了迭代器的循环体，都不要向迭代器所属的容器中添加元素
- 标准库类型限定使用的下标必须是无符号类型；而内置的下标运算无此要求，可以处理**负值**

## 第四章 表达式
- 条件运算符和赋值运算符优先级较低，与其它运算符混用时要注意加括号
```
cout<< ( (grade<60) ? "fail" : "pass" );
cout<< (grade<60) ? "fail" : "pass" ;//输出 0 或 1，再返回一个 "fail" 或 "pass"
while ( ( i = getValue() ) != 42 ){}
```

- 在有重载函数的上下文中使用 const\_cast 无可厚非, 但是在其他情况下使用 const\_cast 也就意味着程序存在某种设计缺陷。每次书写了一条强制类型转换语句，都应该反复斟酌能否以其它方式实现相同的目标。

## 第五章 语句
- 悬垂else
```
//该语句并不像缩进的这样工作
if( grade % 10 >= 3)
	if( grade % 10 > 7)
    	cout<<"11\n";
else
	cout<<"22\n";
//正确应该使用括号括起来
if( grade % 10 >= 3)
{
	if( grade % 10 > 7)
    	cout<<"11\n";
}
else
	cout<<"22\n";
```


#### 标准异常
- exception 头文件定义了最通用的异常类 `exception`，它只报告异常的发生，不提供任何额外信息。
- stdexcept 头文件定义了几种常用的异常类
- new 头文件定义了 `bad_alloc` 异常类型
- type\_info 头文件定义了 bad\_cast 异常类型

|类型|说明
|:--:|:--|
|exception|最常见的问题
|runtime\_error|只有在运行时才能检测出的问题
|range\_error|运行时错误: 生成的结果超出了有意义的值域范围
|overflow\_error|运行时错误: 计算上溢
|underflow\_error|运行时错误: 计算下溢
|logic\_error|程序逻辑错误
|domain\_error|逻辑错误: 参数相应的结果值不存在
|invalid\_argument|逻辑错误: 无效参数
|length\_error|逻辑错误: 试图创建一个超出该类型最大长度的对象
|out\_of\_range|逻辑错误: 使用一个超出有效范围的值

- 标准库异常类只定义了几种运算, 包括创建或拷贝异常类型的对象, 以及为异常对象赋值
- 我们只能以默认初始化的方式初始化 `exception`、`bad_alloc` 和 `bad_cast` 对象，不允许为这些对象提供初值
- 其它异常类型的行为则恰好相反: 应该使用 string 对象或者 C 风格字符串初始化这些类型的对象, 但是不允许使用默认初始化的方式。当创建此类对象时，必须提供初始值，该初始值含有错误相关的信息
- 异常类型只定义了一个名为 what 的成员函数，该函数没有任何参数，返回 C 风格字符串

## 第六章 函数
- 如果实参数量未知但类型相同，那么可以使用标准库的 `initializer_list` 
- 使用尾置返回类型 `auto func(int t) -> int(*)[10]`,返回一个指针，指向含有10个整数的数组
- 内联函数和constexpr函数可以多次定义，但多个定义必须完全一致。所以一般放到头文件中

### 实参类型转换
> 1. 精确匹配：包括实参类型和形参相同、实参从数组类型转换成对应的指针类型、顶层 const 的添加或删除
> 2. 通过 const 转换实现的匹配
> 3. 通过类型提升实现的匹配
> 4. 通过算术类型转换或指针转换实现的匹配
> 5. 通过类类型转换实现的匹配


## 第七章 类
- 将构造函数声明为`= default` 与 `=delete`
- 委托构造函数
```
class Test
{
	Test(std::string str, int m, double p) : ...{...}
    Test() : Test("", 0, 0){}
}
```

## 第八章 IO库
- IO对象没有拷贝或赋值操作

|类型/函数|说明|
|:--:|:--|
|strm::iostate|strm是一种IO类型, 包括 iostream, fstream, sstream 下的多种类型。iostate 是一种机器相关的类型，提供了表条件状态的完整功能
|strm::badbit|用来指出流已崩溃
|strm::failbit|用来指出一个 IO 操作失败了
|strm::eofbit|用来指出流到达了文件结束
|strm::goodbit|用来指出流未处于错误状态
|s.eof()|若流 s 的 eofbit 置位, 则返回 true
|s.fail()|若流 s 的 failbit 或 badbit 置位, 则返回 true
|s.bad()|若流 s 的 badbit 置位, 则返回 true
|s.good()|若流 s 处于有效状态, 则返回 true
|s.clear()|将流 s 所有条件状态位复位, 将流的状态设置为有效, 返回 void
|s.clear(flags)|根据给定的 flags 标志位, 将流 s 条件状态位复位。flags 的类型为 strm::iostate。返回 void
|s.setstate(flags)|根据给定的 flags，将流 s 条件状态位置位。返回 void
|s.rdstate()|返回流 s 的当前条件状态，返回值类型为 strm::iostate

- unitbuf 操纵符
```
cout << unitbuf;//设置输出操作后都会立刻刷新缓冲区
cout << nounitbuf;//回到正常的缓冲方式
```

## 第九章 顺序容器
|类型|说明
|:--|:--
|vector|可变大小数组。支持快速随机访问。在尾部之外插入/删除可能很慢
|deque|双端队列。支持快速随机访问。在头尾位置插入/删除速度很快
|list|双向链表。只支持双向顺序访问。在list任何位置进行插入/删除都很快
|forward\_list|单向链表。只支持单向顺序访问。在任何位置进行插入/删除都很快
|array|固定大小数组。支持快速随机访问。不能添加或删除元素
|string|与 vector 类似的容器，但只用于保存字符。随机访问块，尾部插入/删除块

- forward\_list 的设计目标是达到与最好的手写链表数据结构相当的性能。因此没有 size 操作，对其它容器 size 是个常量时间的操作。有一个特殊的 before\_begin() 函数
- 向一个 vector、string 或 deque 插入元素都可能会使容器中元素的地址发生改变，从而使迭代器、指针、引用失效
- 删除 deque 中除首尾位置之外的任何元素都可能会使容器内元素的地址发生改变；而 vector 或 string 中删除点之后的元素都会向前移动，从而使迭代器、指针、引用失效

> 1. 除非你有很好的理由选择其他容器，否则就应使用 vector。
> 2. 如果你的程序有很多小的元素，且空间的额外空间很重要，则不要使用 list 或 forward_list。
> 3. 如果程序要求岁就访问元素，赢使用 vector 或 deque。
> 4. 如果程序要求在容器的中间插入或删除元素，应使用 list 或 froward_list。
> 5. 如果程序需要在头尾位置插入或删除元素，但不会在中间位置进行插入或删除操作，则使用 deque。
> 6. 如果程序只有在读取输入时才需要在容器中间插入元素，随后需要随机访问元素，则
>> 1. 首先，确定是否真的需要在容器中间位置添加元素。当处理输入数据时，通常可以很容易地向 vector 追加数据，然后再调用标准库的 sort 函数来重排容器中的元素，从而避免在中间位置添加元素。
>> 2. 如果必须在中间位置插入元素，考虑在输入阶段使用 list， 一旦输入完成，将 list 中的内容拷贝到一个 vector 中。


|容器类型别名|说明
|:-|:-
|iterator|此容器的迭代器类型
|const\_iterateor|不能改变元素的迭代器
|size\_type|无符号整型, 用于确定最大大小
|difference\_type|带符号整型, 足够保存两个迭代器之间的距离
|value\_type|元素类型
|reference|元素的左值类型; 与 value\_type& 含义相同
|const\_reference|元素的 const 左值, ( 即 const value\_type & )

- 除 array 外, 其它容器的 swap 都在常量时间内完成。而 array 的 swap 是真正交换了每一个元素，需要线性时间

##### insert 函数
- c.insert(p, t)
- c.insert(p, n, t)
- c.insert(p, b, e)
- 都返回新添加的的第一个元素的迭代器

##### emplace 函数
- c.emplace(p, args)
- c.emplace\_front(args)
- c.emplace\_back(args)
- 会首先根据 args 调用元素类型的构造函数。

#### 容器级别的关系运算符
```
using vint = vector<int>;//功能与使用 typedef 相同
vint v1 = {1,3,5,7,9,12};
vint v2 = {1,3,9};
vint v3 = {1,3,5,7};
vint v4 = {1,3,5,6,7,12};
v1 < v2;//true;
v1 < v3;//false
v1 == v4;//true
v1 == v2;//false
```

#### 额外的 String 操作
- string s(cp, n) 从 cp 开始的 n 个字符， cp 可以是 char \* 也可以是 string
- string s(s2, pos) 从 string s2 的 pos 位置开始, pos 不能大于 s2 的 size，否则抛出 out\_of\_range 异常
- string s(s2, pos2. len2) 从 string s2 的 pos 开始, len2 个, pos2 不能大于 s2 的size，否则抛异常
- string str2 = str.substr(pos, len) 从 str 中 pos 开始, 拷贝 n 个字符。pos 默认值为0，len 默认值为 str.size()-pos。开始位置大于 size 的话会抛异常，拷贝最多到 str 的结束

## 第十章 泛型算法
- 大多数算法都定义在 algorithm 头文件中。标准库还在头文件 numeric 中定义了一组数值泛型算法
- 迭代器令算法不依赖与容器，但算法依赖与元素类型的操作。元素类型需要定义了算法中依赖的 函数、操作符等
- 用一个单一迭代器表示第二个序列的算法都假设第二个序列至少与第一个一样长。确保算法不会越界使程序员的责任
- fill( vec.begin(), vec.end(), 0 ) 使用两个迭代器确定范围
- fill\_n( vec.begin(), n, val ) 使用一个迭代器和一个 n 确定范围

```
  template<typename _InputIterator, typename _Tp>
    inline _InputIterator
    find(_InputIterator __first, _InputIterator __last,
	 const _Tp& __val)
    {
      // concept requirements
      __glibcxx_function_requires(_InputIteratorConcept<_InputIterator>)
      __glibcxx_function_requires(_EqualOpConcept<
		typename iterator_traits<_InputIterator>::value_type, _Tp>)
      __glibcxx_requires_valid_range(__first, __last);
      return std::__find_if(__first, __last,
			    __gnu_cxx::__ops::__iter_equals_val(__val));
    }
```


##### back_inserter
- 后面还有两种插入迭代器
- 位于头文件 iterater 中
- `auto it = back_inserter( vec )` 返回一个绑定容器的插入迭代器，为插入迭代器相当于容器调用 push\_back

##### copy
- `copy( begin(a1), end(a1), dest)`; 把 a1 的内容拷贝到 dest, 需确保 dest 空间足够

##### replace
- `replace( ilst.begin(), ilst.end(), 0, 42)` 把所有 0 替换为 42
- `replace_copy( ilst.cbegin(), ilst.cend(), back_inserter( ivec ), 0, 42)` 不改变 ilist, 将 0 变为 42 后保存在 ivec 中

##### sort
- `sort( vec.begin(), vec.end() )` 默认使用 `<`运算符来实现, 从小到大排序

##### unique
- `auto endUnique = unique( vec.begin(), vec.end() )` 返回最后一个不重复元素之后的位置, 不是成员函数, 不能删除值


### 向算法传递函数
- 谓词 - 一个可调用的表达式
- 一元谓词 - 接受一个参数
- 二元谓词 - 接受两个参数
- **接受谓词参数的算法对输入序列的元素调用谓词。因此，元素类型必须能转换为谓词的参数类型。**

##### partition
- `auto boundry = partition( vec.begin(), vec.end(), function)` 接受一元谓词，将调用谓词为真的元素放到前面，为假的放后面；放回二者的边界

##### find_if
- `auto it = find_if( vec.begin(), vec.end(), fuction)` 返回第一个满足 function 的元素，或者返回 vec.end()

### lambda 表达式
- 因为谓词的参数都是使用的容器中的元素，无法向谓词传入另外的参数来改变判断条件
- 一个 lambda 表达式表示一个可调用的代码单元。可以理解为一个未命名的内联函数
- `\[ *capture* \] ( *parameter list* ) -> *return type* { *function body* }`
- 可以忽略参数列表和返回类型，但必须永远包含捕获列表和函数体 `auto f = \[] { return 42}`；忽略括号和参数列表相当于是空的参数列表；忽略返回类型则根据代码推断：如果只有一个 return 语句，则从返回的表达式推断；如果不止一个语句，则返回类型为 void
- lambda 不能有默认参数
- **后文中的 words 均为 vector<string> 类型**
```
stable_sort( words.begin(), words.end(), 
		[](const string& a, const string& b) { return a.size() < b.size(); } );
//使用捕获列表
std::size_t sz = 10;
auto wc = find_if( words.begin(), words.end(), 
		[sz] ( const string& a, const string& b) { return a.size()>sz; } );
for_each( wc, words.end(), 
		[] (const string& s) { cout<<s<<"\n"; } );
```
> for_each 中捕获列表为空，是因为我们只对 lambda 表达式所处于的外部函数中定义的（非 static）变量使用捕获列表。一个 lambda 表达式可以直接使用定义在当前函数之外的名字。如 cout

- 捕获的变量是在 lambda 表达式创建时拷贝，而不是调用时拷贝
- 可以通过引用捕获 `for_each( words.begin(), words.end(), [ &os, c ] ( const string& s ) { os << s << c ;} )`
- 我们应该尽量减少捕获的数据量，来避免潜在的捕获导致的问题。而且，如果可能的话，应该避免捕获指针或引用。因为在 lambda 执行时，指针或引用的值可能已经不是我们所期望的了。

#### 隐式捕获
- [ = ] 表示隐式通过值捕获
- [ & ] 表示隐式通过引用捕获
```
//隐式通过引用捕获除了 c 以外的值
for_each( words.begin(), words.end(), 
		[ &, c ] ( const string& s ) { os<< s<< c; } )
//隐式通过值捕获除了 os 以外的值
for_each( words.begin(), words.end(), 
		[ =, &os ] ( const string& s ) { os<< s<< c; } )
```


#### 可以改变参数的 lambda
- 默认情况下，对于一个值被拷贝的变量，lambda 不会改变其值。如果需要，则需加上
```
int v1 = 5;
auto f = [=]() mutable { cout<<vi++;};
f();
```

#### 指定 lambda 返回类型
- 不止一个语句时，需显示指明返回类型
```
auto f = [] (int i) -> int { if(i<0) return -i; else return i ;}
```

### 参数绑定
- 如果 lambda 的捕获列表为空，通常可以用函数来代替它。
- 但是，对于捕获了局部变量的 lambda，用函数来替换它就不是那么容易了。

#### 标准库 bind 函数
- 头文件 functional 中
- 可以把 bind 看做一个函数适配器，接受一个可调用对象，生成一个新的可调用对象来“适应”原对象的参数列表
```
bool checkSize( const string& s, string::size_type sz )
{
	return s.size() >= sz;
}
std::size_t sz = 10;
//占位符所在的名称空间
using namespace std::placeholders;
// _1 代表一个占位符，sz 是 checkSize 的第二个参数，区别与前面使用的 lambda 
auto wc = find_if( words.begin(), words.end(), 
		bind( checkSize, _1, sz );
```


- 用 bind 重排参数顺序
```
bool isShorter( const string& a, const string& b)
{
	return a.size() < b.size();
}
sort ( words.begin(), words.end(), bind(isShorter, _2, _1) );
```

- 绑定引用参数 `ref` 与 `cref`
```
ostream & print (ostream & os, const string& s, char c)
{
	return os << s<< c;
}
// ref(os) 表示是 os 的引用；cref 是 const 引用
for_each( words.begin(), words.end(), bind(print, ref(os), _1, ' '));
```

### 插入迭代器
|类型|说明
|:--|:--|
|back\_inserter|创建一个使用 push\_back 的迭代器
|front\_inserter|创建一个使用 push\_front 的迭代器
|inserter|创建一个使用 insert 的迭代器。此迭代器接受第二个参数，这个参数必须是指向给定的容器的迭代器。元素插入到给定迭代器所表示的元素前面

- 如果 it 是 inserter 类型的迭代器，则 
```
*it = val;
```
等同于
```
it = c.insert( it, val);//it指向之前新插入的元素
++it;//递增使其仍指向之前的元素
```

- front\_inserter 生成的迭代器的行为与 inserter 生成的迭代器完全不一样
```
list<int> lst = {1,2,3,4};
list<int> lst2, lst3;
//拷贝完成后, lst2 包含 4 3 2 1
copy( lst.begin(), lst.end(), front_inserter(lst2) );
//拷贝完成后, lst3 包含 1 2 3 4
copy( lst.begin(), lst.end(), inserter(lst3, lst3.begin()));
```

### iostream 迭代器
#### istream_iterator
- 必须指定迭代器将读写的对象类型
- istream\_iterator 使用 >> 来读取流,因此类型必须定义了输入运算符
```
istream\_iterator<int> in\_iter(cin), eof;//从 cin 读取 int, 不初始化就代表 eof
vector<int> vec(in\_iter, eof);//从迭代器范围构造 vec
```

#### ostream\_iterator
- 创建 ostream\_iterator 时, 可以提供(可选的)第二个参数, 在输出每个元素后都会打印此字符
- 必须将 ostream\_iterator 绑定到一个指定的流, 不允许空的或表示尾后位置的
```
ostream_iterator<int> out_iter(cout, " ");
for( auto e : vec)
	*out_iter++ = e;//输出流也可以省略 * 和 ++, 不过不省略更易理解
cout << endl;
//或者
copy( vec.begin(), vec.end(), out_iter);//强大
cout<<endl;
```

### 反向迭代器
- forward\_list 和 流迭代器 不支持 递减 运算, 所以不能创建反向迭代器
```
//找到从最后开始的第一个逗号
auto rcomma = find( line.crbegin(), line.crend(), ',');
//先将 反向迭代器 转换为正向的
cout << string( rcomma.base(), line.cend() ) <<endl;
```

### 泛型算法结构
- 大多数算法都具有如下4中形参之一:
```
alg( beg, end, *other arg*);  
alg( beg, end, dest, *other arg*);  
alg( beg, end, beg2, *other arg*);  
alg( beg, end, beg2, end2, *other arg*);  
```


- beg 和 end 指出了算法的输入范围
- 算法都假定目标空间足够容纳写入的数据
- 更常见的情况是, dest 被绑定到一个插入迭代器, 或是一个 ostream\_iterator , 插入迭代器会将新元素添加到容器中, ostream\_iterator 会将数据写入一个流中, 都可以保证空间是足够的。
- 接受第二个输入序列的算法通常用两个范围中元素结合来进行一些运算

#### 算法命名规范
- 一些算法使用重载形式传递一个谓词
```
unique( beg, end);//使用 == 运算符比较元素
unique( beg. end, comp);//使用 comp 比较元素
```


- \_if 版本的算法, 接受一个谓词
```
find( beg, end, val);//查找输入范围中 val 第一次出现的位置
find_if( beg, end, pred);//查找第一个令 pred 为真的元素
```


- 区分拷贝元素的版本和不拷贝的版本
```
reverse( beg, end );//反转输入范围中元素顺序
reverse_copy( beg, end, dest);//将元素逆序拷贝到 dest
remove_if( beg, end, [](int i) { return i%2; } );
remove_copy_if( beg, end, back_inserter(vec2), [](int i) { return i%2; } )
```


### 特定容器算法
- 通用的 sort 要求随机访问迭代器, 因此不能用于 list 和 forward\_list, 因为这两中容器分别提供双向迭代器和前向迭代器
- 对于 lsit 和 forward\_list, 应该优先使用成员函数版本的算法, 而不是通用算法

|函数|说明
|:--|:--
|lst.merge(lst2)|将来自 lst2 中的元素移动到 lst，lst2将变为空。二者都必须是有序的。使用默认的 `<`运算符
|lst.merge(lst2, comp)|将 lst2 中的元素移动到 lst, 使用自定以的 comp 比较
|lst.remove(val)|调用 erase 删掉与给定值相等==的元素
|lst.remove\_if(pred)|删掉满足 pred 的元素
|lst.reverse()|反转 lst 中元素的顺序
|lst.sort()|使用 `<`排序元素
|lst.sort(comp)|根据 comp 排序元素
|lst.unique()|调用 erase 删除同一个值的连续拷贝, 使用 ==
|lst.unique(pred)|使用一个二元谓词

- lst.splice(args) 或 flst.splice\_after(args)

|参数列表|说明
|:--|:--
|(p, lst2)|p 是指向 lst 中元素的迭代器, 或一个指向 flst 首前位值的迭代.函数将 lst2 中所有元素移动到 lst 中 p 之前或是 flst 中 p 之后。lst2 的类型必须与 lst 或 flst 相同，且不能是同一个
|(p, lst2, p2)|p2 是一个指向 lst2 中位置的有效迭代器。将 p2指向的元素移动到 lst 中， 或是将 p2 之后的元素移动到 flst 中。 lst2 可以与 lst 或 flst 相同。
|(p, lst2, b, e)|b 和 e 必须表示 lst2 中的合法范围。将给定范围中的元素从 lst2 移动到 lst 或 flst。lst2 与 lst或（flst）可以是相同链表，但 p 不能指向给定范围中元素

- 链表特有操作会改变容器
> 多数链表特有的算法都与通用版本很相似，但不完全相同。链表特有版本与通用版本间的一个至关重要的区别是链表版本会改变底层的容器。例如，remove 的链表版本会删除指定的元素。unique 的链表版本会删除第二个和后继的重复版本。  
> 类似的，merge 和 splice 会销毁其参数。例如，通用版本的 merge 将合并的序列写到一个给定的目的迭代器；两个输入序列是不变的。而链表版本的 merge 会销毁给定的链表——元素从参数指定的链表中删除，被合并到调用 merge 的链表对象中。在 merge 之后，来自两个链表中的元素仍然存在，但它们都已在同一个链表中。

## 第十一章 关联容器
- 关联字类型的要求
> 对于有序容器——map、multimap、set以及multiset，关联字类型必须定义元素比较的方法。默认情况下，标准库使用关键字类型的 < 运算符来比较两个关键字。

- 在实际编程中，重要的是，如果一个类型定义了“行为正常”的`<`运算符，则它可以用作关键字类型
- 对 map 而言，value\_type 是一个 pair 类型, 其 first 成员保存 const 的关键字, second 保存值
- set 的迭代器是 const 的
- 我们通常不对关联容器使用泛型算法
- 关联容器定义了名为 find 成员
- 在实际编程中, 如果我们真要对一个关联容器使用算法, 要么是将它作为一个源序列, 要么当做目的位置
- 检测 insert 的返回值
> insert 或 emplace 返回的值依赖与容器类型和参数。对于不包含重复关键子的容器，添加单一元素的 insert 和 emplace 版本返回一个 pair，告诉我们插入操作是否成功。pair 的 first 成员是一个迭代器，指向具有给定关键字的元素；second 成员是一个 bool 值，指出元素是插入成功还是已经存在与容器中。如果关键字已在容器中，则 insert 什么也不做，且返回值中 bool 部分为 false。如果关键字不存在，元素被插入容器中，且 bool 值为 true。

- 严格弱序: 关联容器所使用的关键字见的关系。在一个严格弱序中，可以比较任意两个值并确定哪个更小。若任何一个都不小于另一个，则认为两个值相等。

## 第十二章 动态内存
- 以下三种指针都在 memory 头文件中
- shared\_ptr - 允许多个指针指向同一个对象
- unique\_ptr - “独占”所拥有的对象
- weak\_ptr  - 一种弱引用,指向 shraed\_ptr 所管理的对象

### shard\_ptr
- make\_shared < type>(var)
- 不要混合使用普通指针和智能指针；此外，智能指针定义了一个名为 get 的函数，不要用这个函数获得的指针去初始化另一个智能指针或为智能指针赋值，会两次 delete
- p.unique() 查询是否是唯一用户
- p.reset( new string(\*p) ) 分配新副本
- 创建一个 shared\_ptr 时, 可以传递一个指向删除器函数的参数

### unique_ptr
- 初始化 uniq\_ptr 必须采用直接初始化形式
- u.release() 放弃对指针的控制权, 返回指针, 并将 u 置空
- unique\_ptr 的删除器定义方式与 shared\_ptr 不一样, 需要将类型写入模板参数中 `unique\_ptr<objT, delT> p ( new objT, fcn )`

### weak_ptr
- 不影响 shared\_ptr 的计数
- 因为可能不存在,所以需要验证是否还存在。 `wp.lock()`，不存在了则返回 false，仍存在则为 true

### 动态数组
- 动态数组不是数组类型，不能调用 begin 或 end
- unique\_ptr 可以自动对动态数组调用 `delete [] p`
- 但如果用 shared\_ptr 管理动态数组, 必须自己定义一个删除器,可以使用 lambda 
`shared_ptr<int> sp ( new int[10], [] (int* p) { delete[] p; });`   
不提供删除器则默认调用的是 `delete p`
- shared\_ptr 也不支持 \[ ] 运算符  
- 一种替代方法, 很不优雅, 所以最好还是使用标准库的容器  
`for( siez_t i = 0; i != 10; ++i ) { *( sp.get()+i ) = i ;}`

### allocator 类
- 定义在 memory 头文件中

|函数|说明|
|:--|:-|
|allocator < T> a|定义一个名为 a 的对象, 可以为类型 T 的对象分配内存
|a.allocate( n )|分配一段原始的、未构造的内存，保存 n 个类型为 T 的对象, 必须再调用 constructor
|a.deallocate( p, n)|释放从 T\* 指针 p 中的地址开始的内存, 这块内存保存了 n 个类型为 T 的对象; p 必须是一个先前由 allocate 返回的指针, 且 n 必须是 p 创建时所要求的大小。在调用 deallocate 之前，用户必须对每个在这块内存中创建的对象调用 destory
|a.constructor(p, args)|p 必须是一个 T\* 指针, 指向一块原始内存; arg 被传递给类型为 T 的构造函数, 用来在 p 指向的内存中构造一个对象
|a.destory(p)|p 为 T\* 类型的指针, 此算法对 p 指向的对象执行析构函数

```
int n = 4;
allocator<string> alloc;
auto const p = alloc.allocate(n);
auto q = p;
alloc.constructor(q++);//第一个, 空字符串
alloc.constructor(q++, 10, 'c');//第二个, cccccccccc
alloc.constructor(q++, "hi");//第三个, hi
while ( q != p )
	alloc.destory( --q);
```

- 只能对真正构造了的元素调用 destory 函数

### 拷贝和填充未初始化内存的算法
- 位于头文件 memory 中

|函数|说明
|:-|:-
|uninitialization\_copy( b, e, b2 )|从 b 和 e 的范围中拷贝到 b2 指定的未构造原始内存中， 返回一个指向最后一个构造元素之后的位置
|uninitialization\_copy\_n( b, n, b2 )|从 b 开始, 拷贝 n 个到 b2
|uninitialization\_fill( b, e, t)|在 b 和 e 范围内创建对象, 均为 t 的拷贝
|uninitialization\_fill\_n( b, n, t)|从 b 开始, 创建 n 个对象, 都为 t 的拷贝\_

## 第十三章 拷贝控制
- 拷贝构造函数的自己的参数必须是引用类型 - 如果按值传递, 那么需要先拷贝一份, 而拷贝一份需要拷贝构造函数, 无限自己调用自己
- 赋值运算符应该返回一个指向其左侧运算对象的引用
- 需要析构函数的类也需要拷贝和赋值的操作
- `= default` 显示地要求编译器生成默认版本
- `= delete` 删除该函数, 不能删除析构函数
- 赋值运算符必须能处理自赋值的情况
- 对于那些重排元素顺序的算法, 定义 swap 函数是非常重要的
- copy and swap
```
HasPtr& HasPtr::operator = ( HasPtr rhs )
{
	//需要 swap 函数, 可以自定义一个高效的 swap, 天然就是异常安全的, 且处理了自赋值的情况
	swap( *this, rhs);
    return *this;
}
```



### 右值引用
- 一些标准库里的类, 包括 string, 都定义了移动构造函数, 不复制底层内存, 只是赋值指向这斜内存的指针。且保证被移动的对象仍处于可析构的状态，但不保证其中的值。
- std::move 定义在 utility 头文件中
- 使用 && 表示右值引用, & 表示左值引用。左值引用不能绑定到要求转换的表达式、字面值常量或是返回右值的表达式。右值引用具有完全相反的绑定特性，可以将右值引用绑定到这类表达式上，但不能将右值引用直接绑定到一个左值上
```
int i = 42;
int &r = i;//ok
int &&rr = i;//错误
int & r2 = i * 4;//错误
const & r3 = i * 4;//ok
int && rr2 = i * 4;//ok
int && rr3 = rr2;//错误, 表达式 rr2 是左值
```


- 左值持久, 右值短暂
- 由于右值只能绑定到临时对象, 我们得知
	- 所引用的对象将要被销毁
	- 该对象没有其他用户


- 右值表示临时对象, 变量是左值, 因而不能将一个右值引用直接绑定到一个变量上

#### std::move 函数
- 显示地将一个左值转换为对应的右值引用.  
`int && rr3 = std::move(rr1);` 相当于告诉编译器, 我们有一个左值, 但我们希望像一个右值一样处理它。我们必须认识到，调用 move 就意味着承诺: 除了对 rr1 赋值或销毁它以外, 我们将不再使用它。调用 move 之后，不能对源对象中的值做任何假设。

#### 移动构造函数和移动赋值运算符
```
StrVec::StrVec( StrVec && s ) noexcept :
	elements( s.elements )
{
	s.elements = nullptr;
}
```

- 不抛出异常的移动构造函数和移动赋值运算符必须标记为 noexcept。告诉**标准库**我们的移动构造函数不会抛出异常。函数的声明和定义处**都需要添加 noexcept**
- 两个相互关联的事实：
	1. 首先，虽然移动构造函数通常不抛出异常，但抛出异常也是允许的
	2. 标准库容器能对异常发生时其自身的行为提供保障。例如，vector 保证，如果我们调用 push\_back 时发生异常，vector 自身不会发生改变。


- 所以，除非 vector 知道元素类型的移动构造函数不会抛出异常，否则在重新分配内存的过程中，它就必须使用拷贝构造函数而不是移动构造函数。以避免只移动了一半元素的情况。如果希望在 vector 重新分配内存这类情况下对我们自定义类型的对象进行移动而不是拷贝，就必须显式地告诉标准库我们的移动构造函数可以安全使用。
- 移动以后源对象仍然可析构，但不能对其值做任何假设
- 如果一个类定义了自己的拷贝构造、拷贝赋值或析构函数，编译器不会为它合成移动构造函数和移动赋值运算符。只有当一个类没有定义任何自己版本的拷贝构造控制成员，且类的每个非 static 成员都可以移动时，编译器才会为它合成移动构造函数或移动赋值运算符。
- 移动操作永远不会隐式定义为删除的函数。但如果我们显式地要求编译器生成 `= default` 的移动操作，且编译器根本不能移动所有成员，则编译器会将移动操作定义为删除的。
##### 建议
> 所有五个拷贝控制成员应该看做一个整体: 一般来说, 如果一个类定义了任何一个拷贝操作, 它就应该定义所有五个操作。如前所述，某些类必须定义拷贝构造函数、拷贝赋值运算符和析构函数猜能正确工作。这些类通常拥有一个资源，而拷贝成员必须拷贝此资源。一般来说，拷贝一个资源会导致一些额外的开销。在这种拷贝并非必要的情况下，定义了移动构造函数和移动赋值运算符的类就可以避免此问题。  
> 不要随便使用移动操作。除非你确认了源对象没有任何其他用户。  
> 通过在类代码中小心地使用 move，可以大幅度提升性能。而如果随意在普通用户代码（与类实现代码相对）中使用移动操作，很可能导致莫名奇妙的、难以查找的错误，而难以提示应用程序性能。


- 一般来说，我们不需要为函数操作定义接受一个  `const T &&` 或是 `X &` 的版本。因为如果要“窃取”值，就传递一个右值引用，不能是 const 的；而从一个对象的拷贝操作不应该改变该对象，所以不需要 非const 版本

#### 引用限定符
```
class Foo
{
	Foo  & operator = (const Foo & ) &;
};
Foo & Foo::operator = (const Foo & ) &
{
	.....
	return *this;
}
```

- 例子中函数的声明和定义最后的 `&` 是引用限定符，声明和定义都需要加上引用限定符
- 只能用于非 static 成员函数
- 对于 `&` 限定的函数，只能把函数返回值当左值使用；对于 `&&` 限定的函数，只能把函数当做右值使用
- 如果定义了两个名字和参数列表相同的成员函数，那么要么都加上引用限定符，要么都不加


## 第十四章 重载运算与类型转换
- 输入运算符必须处理输入可能失败的情况。且赢负责从错误中回复
- 通常情况下应该使用复合赋值来实现算术运算符
- 定义了 `==` 通常也应该定义 `！=`，且 `==` 运算符应该具有传递性
- 定义的 `<` 运算符和定义的 `==` 运算符产生的结果的逻辑应当一致, 不一致时不要轻易定义 `<`
- 下标运算符两个版本, const 和非 const
- 递增递减运算符, 后置版本应返回一个值而非引用
```
class StrBlobPtr
{
	StrBlobPtr & operator ++ ();//前置
    StrBlobPtr operator ++ (int);//后置
	StrBlobPtr & operator -- ();//前置, 返回引用
    StrBlobPtr operator -- (int);//后置, 返回值
};
```

### 函数调用运算符
- 函数对象通常作为泛型算法的实参
- 重载的 `operator ()` 用于接受容器中的元素, 而类的其它成员可以随时绑定为其它值
```
auto wc = find_if( words.begin(), words.end(), 
		[sz] ( const string& a ) { return a.size()>= sz;} );
//用函数对象表示:
class SizeComp
{
	SizeComp( size_t n): sz(n) {}
    bool operator () (const string& s) const
    { return s.size() >= sz;}
private:
    size_t sz;
};
auto wc2 = find_if( words.begin(), words.end(), SizeComp(sz)  );
```

#### 标注库定义的函数对象
|算术|关系|逻辑
|:-|:-|:-
|plus< Type >|equal\_to< Type >|logical\_and< Type >
|minus< Type >|not\_equal< Type >|logical\_or< Type >
|multiplies< Type >|greater< Type >|logical\_not< Type >
|divides< Type >|greater\_equal< Type >
|modulus< Type >|less< Type >
|negate< Type >|less\_equal< Type >

- 标准库规定其算法对象对于指针同样使用, 可比较两个指针的地址的大小

#### 可调用对象与
函数、函数指针、lambda 表达式、bind 创建的对象以及函数对象
#### 标准库 function 类型
```
function< int(int, int) > f1 = add;
function< int(int, int) > f2 = divide;
function< int(int, int) > f3 = []( int i, int j ) { return i*j; };
```

- 配合 `map` 构建函数表
- function 不能直接将重载函数的名字存入 function 对象中，可以用函数指针作为参数，而不是直接使用函数名字。也可以使用 lambda 来消除二义性

#### 类型转换
- 如果在调用重载函数时我们需要使用构造函数或者强制类型转换来改变实参类型，则这通常意味着程序的设计存在不足

## 第十五章 面向对象程序设计
- 可以在类的声明处用 final 关键子来防止该类被继承
- 成员函数可以声明为 final 或 override 来显示地表明 该函数不希望被重写 或 重写基类的该函数，编译器会强制这两个关键字的实施
- 除了覆盖继承而来的虚函数以外，派生类不要重写其它定义在基类中的名字
- 一如往常，名字查找优先于类型检查
- 派生类中的函数只会覆盖基类的同名函数，而不会重载，可以再在派生类内提供一条 using 声明来使基类的函数不被覆盖

## 第十六章 模板与泛型编程

## 第十七章 标准库特殊设施
#### tuple
- 头文件 tuple
- 类似 pair，但 pair 只有两个，tuple 可以有 n 个

#### bitset
- 头文件 bitset
- 且能处理超过最长整型类型大小的位集合

#### 正则表达式
- 头文件 regex 
- 一个正则表达式的语法是否正确是在运行时解析的
- 构造一个 regex 对象以及向一个已经存在的 regex 赋予一个新的正则表达式可能是非常耗时的
- 使得位运算的使用更为容易

|类型/函数|说明
|:-|:-
|regex|一个正则表达式的类型

regex\_match 与 regex\_search 的参数说明

- ( seq, m, r, mft ) 
- ( seq, r, mft )  
在 seq 中查找regex对象 r 中的正则表达式。seq 可以是 string、表示范围的一对迭代器以及一个以空字符结尾的字符数组的指针。  
m 是一个 match 对象，用来保存匹配结果的相关细节。m 和 seq 必须具有兼容的类型。mft 是一个可选的 regex\_constants::match\_flag\_type 值。

#### 随机数
- 头文件 random
- 当我们说随机数发生器时, 是指分布对象和引擎对象的组合
##### 随机数引擎
```
Engine e;//Engine 只是一个简写, 实际类型有好几种, 也长的多
Engine e(s);//s作为种子
e.seed(s);//使用s作种子
e.min();//此引擎生成的最小值
e.max();//最大值
Engine::result_type;//此引擎生成的 unsigned 整型
e.discard(u);//将引擎推进 u 步, u 类型为 unsigned long long
```

##### 随机数分布
```
uniform_int_distribution< int > u (0, 9 );// 0-9 间的随机数分布
defualt_random_engine e;//生成无符号随机整数
int a = u(e);//一个分布在 u 里的随机数
int b = e();
uniform_real_distribution<double> u2(0, 1 );// 0-1(包括) 之间的均匀分布
normal_distribution<> n (4, 1.5);//均值4, 标准差 1.5 的正态分布
bernoulli_distribution b;//伯努利分布, 默认50%概率返回false, 50%返回 true
bernoulli_distribution b(0.55);//返回 true 的概率是0.55
```

#### IO 库再探
##### boolalpha
- `cout << true <<"\t"<< boolalpha << true;`将先输出1, 再输出 true
- 影响之后的所有输出, 直到遇到下一个
```
// 输出 true true 1
cout << boolalpha << true<<" ";
cout << true << noboolalpha <<" "<< true;
```

##### 指定进制
- 同样也影响之后的所有输出, 直到遇到下一个改变
```
cout << oct << 20 <<" "<< 1024 <<endl;
cout << dec << 20 <<" "<< 1024 <<endl;
cout << hex << 20 <<" "<< 1024 <<endl;
```

- 输出进制信息
```
cout << showbase;
cout << oct << 20 <<" "<< 1024 <<endl;
cout << dec << 20 <<" "<< 1024 <<endl;
cout << hex << 20 <<" "<< 1024 <<endl;
cout << noshowbase;
```

#### 定义在 iostream 中的操纵符
\* 号代表默认的流状态

|符号|说明
|:-|:-
|boolalpha|将 true 和 false 输出为字符串
|\* noboolalpha| 将 ture 和 false 输出为 1 和 0
|showbase|对整型值输出进制
|\* noshwobase|不输出进制
|showpoint|浮点数总是显示小数点
|\* noshwopoint|只有小数部分部位0时才显示小数点
|showpos|对非负数显示 +
|\* noshowpos|对非负数不显示 +
|uppercase|在十六进制中打印 0X, 科学计数法中打印 E
|\* nouppercase|在十六进制中打印 0x, 科学计数法中打印 e
|\* dec|整型显示为十进制
|hex|十六进制
|oct|八进制
|left|值的右侧添加填充字符
|right|左侧添加
|internal|在符号和值之间添加填充字符
|fixed|浮点值为定点十进制
|scientific|浮点值显示为科学计数法
|hexfloat|浮点值显示为十六进制
|defaultfloat|重置浮点值格式为十进制
|unitbuf|每次输出操作都刷新缓冲区
|\* nounitbuf|恢复正常缓冲区刷新方式
|\* skipws|输入运算符跳过空白符
|noskipws|输入运算符不跳过空白符
|fulst|刷新 ostream 缓冲区
|ends|插入空字符, 然后刷新 ostream 缓冲区
|endl|插入换行, 然后刷新 ostream 缓冲区

- 除非你需要控制浮点数的表示形式( 如, 按列打印数据或打印表示金额或百分比的数据), 否则由标准库选择计数法是最好的方式

##### 定义在 iomanip 中的操纵符
|操纵符|说明
|:-|:-
|setfill( ch )|用 ch 填充空白, **影响整个流**
|setprecision( n )|将浮点数精度设置为 n, **影响整个流**
|setw( n )|读或写值的宽度为 w 个字符, **只影响下一个**
|setbase( b )|整数输出为 b 进制, **影响整个流**

##### 流随机访问
- 标准库只维护单一的标记——并不存在独立的读标记和写标记。对于一个既能读又能写的流，写标记和读标记是一样的，所以必须用 seek 来重定位
- tellg() 返回一个输入流标记的当前位置
- tellp() 返回一个输出流标记的当前位置
- seekg( pos ) 在输入流中将标记重定向到指定位置。pos 通常是前一个 tellg 或 tellp 的返回值
- seekp（ pos ） 在输出流中将标记重定向到 pos
- seekg(off, from ) 在输入流中将标记重新定位到 from 之前或之后 off 个字符
- seekp(off, from ) 在输入流中将标记重新定位到 from 之前或之后 off 个字符
	- from 可以是下列值之一
	- beg，off 相对于流开始位置
	- cur，相对于当前位置
	- end，相对与流结尾位置


## 第十八章 用于大型程序的工具
#### 异常类

|exception|
|:-:|:-:|:-:|:-:|
|bad\_cast|runtime\_error|logic\_error|bad\_alloc

|runtime\_error|
|:-:|:-:|:-:
|overfolw\_error|underflow\_error|range\_error

|logic\_error|
|:-:|:-:|:-:|:-:|
|domain\_error|invalid\_argument|out\_of\_range|length\_alloc

#### 命名空间
- 命名空间可以是不连续的
- 可以在多个文件中为命名空间添加成员
- 命名空间的别名 `namespace N2 = N;` 将 N2 代表 N

##### 内联命名空间
- 可以直接被外部使用, 无需显示值出命名空间的

##### 未命名的命名空间
- 相当于 static 函数和变量
- 在文件中进行静态声明的做法已经被 c++ 标准取消了, 现在的做法是使用未命名的命名空间

## 第十九章 特殊工具与技术
#### typeid 运算符
- 同样用于 RTTI
- 当运算对象是定义了至少一个虚函数类型的左值时, typeid 的结果直到运行时才会求得
- 当 typeid 作用于指针时, 返回的结果是指针的静态编译类型, 应当作用于 指针指向的值

#### 类成员指针
- 数据成员指针, 只是代替了类中某数据的用法, 不是指向真正的数据, 对数据成员解引用获得数据成员, 可通过类的对象调用这个解引用获得的数据成员  
```
TypeTest fff;
const string TypeTest::*pData;//可以指向 TypeTest 的对象的 string 成员
pData = &TypeTest::stringVal;
fff.*pData;//正确的用法
class Screen
{
	//返回成员数据指针的静态函数
	static const string Screen::*data()
    { return &Screen::stirngVal ; }
};
```

#### 成员函数指针
- 使用方法与类成员指针类似, 但要注意优先级, 调用运算符的优先级高于指针指向成员运算符的优先级
```
(fff.*pFun)(a, b );
```

#### 将成员函数用作可调用对象
```
function< bool (const string &)> fun = &string::empty;
find_if( svec.begin(), svec.end(), fun);
```

- mem\_fn　生成可调用对象, 让编译器负责推断成员的类型；和 function　不同的是，mem\_fn 可以根据成员指针的类型推断可调用对象的类型，而不用用户显示指定。可以认为它生成的可调用对象接受两种参数： string \* 和 string &
```
auto f = mem_fn( & string::empty );//f接受一个 string 或 string *
auto it = find_if( svec.begin(), svec.end(), f );
```

- 使用 bind, 和 mem\_fn 一样, 也可以是 指针或引用
```
auto it = find_if( svec.begin(), svec.end(), 
		bind( &string::empty, _1 ));
```


#### 链接指示器
- extern "c"
- extern "Ada"
- extern "FORTRAN"
