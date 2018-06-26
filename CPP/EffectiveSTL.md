# Effective STL
## 1.仔细选择你的容器
- 标准 STL 序列容器: vector、string、deque 和 list
- 标准 STL 关联容器: set、multiset、map 和 multimap
- 标准无序容器，C++11 已新增，unordered\_set、unordered\_multiset、unordered\_map 和 unordered\_multimap。为了避开现存的 hash\_\*名字
- 非标准序列容器 slist 和 rope。slist 是单向链表，只能向前，与 forward\_list刚好相反; rope 是一个重型字符串
- 非标准关联容器 hash\_set、hash\_multiset、hash\_map 和 hash\_multimap
- vector< char > 可以作为 string 的替代品
- vector 作为标准关联容器的替代品
- 几种标准非 STL 容器，包括数组、bitset、valarray、stack、deque 和 priority\_queue。值得注意的是，数组可以和 STL 算法配合，因为指针可以当做数组的迭代器使用

## 2.小心对“容器无关代码”的幻想
- 不同的容器是不同的，而且它们的优点和缺点有重大不同。它们并不被设计成可互换的，而且你做不了什么包装的工作。
- 如果有要一次次改变容器类型的必然性，那么你可以用这个常用的方法让改变得以简化: 使用封装。其中一种简单的方法是通过自由地对容器和迭代器类型使用 typedef。

## 4.使用 empty 来检查 size() 是否为0
- 对于 list 而言，调用 splice 可能是常量时间的操作，则调用 size 就必须是线性时间的
- 如果要把 size() 设计为常量时间，就必须把 splice 设计为线性时间，因为 splice 必须计算自己移动的元素的个数。
- 不同的 list 实现用不同的方式解决这个矛盾

## 5.尽量使用区间成员函数代替它们的单元素兄弟

```cpp
int data[numValues];
vector<int> v;
v.insert( v.begin(), data, data+numValues );
//而不是
vector<int>::iterator insertLoc( v.begin() );
for( int i=0; i<numValues; ++i )
{
	insertLoc = v.insert( insertLoc, data[i] );
}
```

使用第二种方法有三种额外开销: 
1. 没必要的函数调用。拷贝 N 个元素需要调用 N 次函数; 内联可能会使你节省这种开销，也可能不会。
2. 无效率地把 v 中现有元素移动到它们最终插入后的位置的开销。每次调用 insert 都会使插入节点后的元素向后移动一次来为新元素腾出空间。而标准要求区间 insert 函数直接把现有元素移动到它们最后的位置，也就是，开销是每个元素一次移动。对于每一个前向迭代器包括数组的指针在内，这很容易做到。唯一不提供前向迭代器能力的标准迭代器是输入和输出迭代器。因此，当传给 insert 区间形式的迭代器是输入迭代器时，区间插入也必须每次一位地把元素移动到它们的最终位置，期望中的优点消失了。
3. 重复使用单元素插入而不是一个区间插入就必须处理内存分配，虽然在它里面也有一个令人讨厌的拷贝。当 vector 重新分配内存时，会把旧内存的元素拷贝到新内存，再销毁旧内存中的元素。而区间插入则只需分配一次。

使用 list 时没有拷贝和内存分配的问题。取而代之的是一个新问题: 过多重复地对 lsit 中一些节点的 next 和 prev 指针赋值。

使用关联容器，几乎没有效率问题，但附加的重复调用 insert 函数的开销问题仍存在。

而使用区间插入，可以键入更少的输入，获得更高的效率。

- 区间构造 `container::container(InputIterator begin, InputIterator end);`
- 区间插入  
`void container::insert( iterator pos, InputIterator begin, InputIterator end );`  
关联容器不需要 pos 参数:  
`void container::insert( InputIterator begin, InputIterator end );`
- 区间删除  
`iterator container::erase( iterator begin, iterator end );`  
关联容器:  
    `void container::erase( iterator begin, iterator end );`
- 区间赋值 `void container::assign( InputIterator begin, InputIterator end );`

## 6. 警惕 C++ 最令人恼怒的解析
```
//警告！这完成的并不是像你想象的那样
ifstream dataFile( "ints.dat" );
list<int> data( istream_iterator<int>(dataFile), istream_iterator<int>() );
```
这段代码可以编译，但在运行时，它什么都没做。它只是声明了一个函数。  
这个函数返回类型是 list< int > 。带有两个参数
- 第一个参数叫 dataFile。它的类型是 istream\_iterator< int >
- 第二个参数没有名字。它的类型是一个**函数指针**，这个函数指针不接受参数，返回 istream\_iterator< int >

这符合 C++ 里的一条通用规则——几乎任何东西都可能被分析成函数声明。
```
class Widget{...};//有默认构造函数
Widget w();
```
声明了一个叫 w 的函数，不接受参数，返回 Widget  
两个解决方法
```
//第一种，使用括号包住这个函数调用
list<int> data( (istream_iterator<int>(dataFile)), istream_iterator<int>()  );
//第二种
istream_iterator<int> dataBegin( dataFile );
istream_iterator<int> dataEnd;
list<int> data( dataBegin, dataEnd );
```
第一种方法的原理是，用括号包住一个实参的声明是不合法的，但包住一个函数调用是合法的，这可能不被某些编译器接受  
第二种方法在哪里都能工作
## 7.使用 new 得指针的容器时，记得在销毁容器前 delete 那些指针
然而，当有异常发生时，这种内存管理也变得很麻烦。
- 首先，不用 for 循环 delete 每一个元素，而是用 for\_each
- 使用智能指针的容器来代替指针的容器

## 9. 在删除选项中仔细选择
- 去除一个容器中有特定值的所有对象:
    - 如果容器是 vector、string 或 deque，使用 erase-remove 惯用法
    - 如果容器是 list，使用 list::remove
    - 如果容器是标准关联容器，使用它的 erase 成员函数

- 去除一个容器中满足一个特定判定式的所有对象:
    - 如果容器是 vector、string 或 deque，使用 erase-remove\_if 惯用法
    - 如果容器是 list， 使用 list::remove\_if
    - 如果是标准关联容器，使用 remove\_copy\_if 和 swap 的低效简单做法，或是一个循环来遍历容器元素，但要记得把迭代器传给 erase 时记得后置递增它。

- 在循环内做某些事情（除了删除对象之外）:
    - 如果是标准序列容器，写一个循环遍历，调用 erase 时记得用它的返回值更新你的迭代器
    - 如果是标准关联容器，写一个循环遍历，把迭代器传给 erase 时记得后置递增它

## 10. 注意分配器的协定和约束
- 把你的分配器做成一个模板，带有模板参数T，代表你要分配内存的对象类型
- 提供 pointer 和 reference 的 typedef，但总是让 pointer 是 T\*，reference 是 T&
- 决不要给你的分配器每对象状态。通常，分配器不能有非静态的数据成员。
- 记得应该传给分配器的 allocate 陈冠函数需要分配的_对象个数_而不是字节数。也应该记得这些函数返回 T\* 指针（通过 pointer typedef），即使还没有 T 对象被构造
- 一定要提供标准容器依赖的内嵌 rebind 模板

写你自己的分配器时你必须做的大部分事情是重现大量样板代码，然后修补一些成员函数，特别是 allocate 和 deallocate。
## 11. 理解自定义分配器的正确用法
## 13. 尽量使用 vector 和 string 来代替动态分配的数组
- STL 用的越多，越会歧视内建的数组
- 很多 string 实现在后台使用了引用计数。C++ 标准委员会特别设法保证了那是一个合法的实现。
- 但 string 的引用计数可能在多线程环境下带来麻烦。string 是一个 basic\string< char > 的 typedef。想要不使用引用计数，可以有三种方法:
    1. 查看库的实现是否可以关闭引用计数。但这是不可移植的
    2. 寻找另外一个 string 实现的替代品
    3. 使用 vector< char > 作为替代品，当然也意味这放弃了 string 的专用成员函数，但大部分功能仍然可以通过 STL 算法得到


## 14. 使用 reserve 避免不必要的内存分配
要注意区分 resize 和 reserve，以及 size 和 capacity。前面的都是数组实际的元素，后面的是数组在不重新分配内存的情况下能容纳的元素
## 16. 如何将 vector 和 string 的数据传给遗留的 API
- 对于 vector 使用 &v\[0\]，但是不要用这个指针修改 vector 中元素的个数！因为1.指针可能越界；2，会使vector的内部状态变得不一致，它再也不知道自己的正确大小了，调用 size 会返回一个不正确的结果。  
- 对于 string 使用函数 c\_str()，因为 string 并不保证数据存储在独立的一块连续内存中，也未承诺内部的形式以一个 null 字符结束。而 c\_str 返回一个C风格字符串
- string 的 c\_str 返回的是 const 指针，如果不能用 const 指针，则可以使用 vector< char > 代替 string

## 17. 使用“交换技巧”来修整过剩容量
`vector< int > ( V ).swap( V );`原理很简单，拷贝构造函数只分配拷贝的元素需要的内存。当然 vector 和 string 可能都需要多一点的空间。上述代码只是“收缩到合适”  
`vector< int >(),swap( V );`清空了 V 并且最小化它的容量
## 18. 避免使用 vector< bool>
- 它并不是有一个 STL 容器
- 它并不容纳 bool

一个东西要成为 STL 容器就必须满足所有在C++标准23.1节中列出的容器必要条件。在这些要求中有这样一条: 如果 c 是一个 T 类型的容器，且 c 支持 operator\[\] ，那么一下代码必须能够编译: `T * p = &c[0]`，但 vector< bool > 并不保存真正的 bool，而是打包 bool 以节省空间。每个保存在 vector 中的 bool 只占用一个比特  
deque< bool > 是一个 STL 容器，它保存真正的 bool 值。当然，deque 内部内存不是联系的，所以不能传递给 C API。也不能让 vector< bool > 做这一点，没有可移植的取得 vector< bool >中数据的方法

-  替代品 bitset
bitset 并不是一个STL容器，但它是C++标准库的一部分。与STL容器不同，它的大小（元素数量）在编译器固定，因此它不支持插入和删除元素。也不支持 iterator。

## 19. 了解相等和等价的区别
- 操作上来说，相等的概念是基于 operator== 的
- 等价是基于一个有序空间中对象值的相对位置，是基于 `operator< `的。即如果两个值都不小于对方，那么它们是等价的
- set 的成员函数 find 使用的等价这个概念，而通用的 find 算法用的是相等这个概念 

## 20. 为指针的关联容器指定比较类型
- 使用关联容器保存指针时要指定比较的函数
- 同时使用时也应注意里面保存的是指针

## 21. 永远让比较函数对相当的值返回 false
- 使用 **小于号** 而不是 **小于等于号**
- 所以 不能简单地对 **小于号** 判断的结果进行取反来获得 **大于号** 的结果
- 用于排序关联容器的比较函数必须在它们所比较的对象上定义一个“严格的弱序化（strict weak ordering），传递给 sort 的算法也有同样限制”

## 22. 避免原地修改 set 和 multiset 的值
- map 和 multimap 的键类型都是 const 的
- 将会破坏容器的有序性，你有权利修改，但必须确保不改变一个*键部分*——影响容器有序性的元素部分。如果你做了，你会破坏那个容器，再使用那个容器将产生未定义的结果，而且那将是你的错误。对于不影响有序性的部分。可以随便更改
- 但是，有些编译器可能不允许修改 set 和 multiset 的值
- 最安全的改变 set、multiset、map 和 multimap 的方法:
    1. 定位想改变的元素，获得迭代器 i
    2. 拷贝一份。对于 map 和 multimap ，不要把副本的第一个元素声明为 const，毕竟是要修改它
    3. 修改副本，使它有你想要在容器里的值
    4. 从容器里删除元素，通常通过调用 erase(i++)，为什么是 i++，参考条款9
    5. 把新值插入容器。如果新容器在排序容器中的位置正好相同或相邻于删除的元素，使用 insert 的“提示”形式把插入的效率从对数时间改进到分摊的常数时间。使用第一步获得的迭代器作为提示


## 23. 考虑使用有序 vector 代替关联容器
- 非标准的散列容器，如果使用了合适的散列函数，可以认为提供了常数时间的查找。优于对数时间查找的 set、map和它们的 multi 同事
- 即使你需要的只是对数时间的查找，标准关联容器仍可能不是你的最佳选择
- 标准关联容器的典型实现是平衡二叉查找树，插入、删除和查找是混在一起的
- 而很多应用中，对数据结构的使用可以总结为三个截然不同的阶段:
    1. 建立。几乎所有操作都是插入和删除
    2. 查找。几乎没有插入和删除
    3. 重组。插入或删除，之后再回到阶段2

- 对于这样使用数据结构的应用来说，一个 vector 可能比一个关联容器提供更高的性能（时间和空间上的都是）。但不是任意 vector 都会，只有有序 vector。有序 vector 可以正确使用查找算法——binary\_search、lower\_bound、equal\_range 等
- 为什么一个有序 vector 的二分查找比一个二叉树的二分查找提供了更好的性能？
    1. 大小问题。关联容器中一个节点的空间开销至少是三个指针。父节点和两个子节点。而 vector 通常共占三个或两个指针和一个int。可以使用较少的内存页面
    2. 引用局部性问题。假如你的STL实现没有改进树节点中的引用局部性，这些节点会分散你所有你的内存空间，会导致更多的页面错误。而 vector 的元素在物理内存上一个挨着一个，正好与二分查找时，一个爱着一个的 vector 页面错误最少

#### 麻烦
- 但是，当然，有序 vector 的大缺点是它必须有序！当新元素插入时，大于该元素的所有东西都必须上移一位，如果 vector 必须重新分配内存，则代价更昂贵。同样删除的代价也很昂贵。所以只有当你知道你的数据结构使用的时候查找几乎不和插入和删除混合使用时，使用有序 vector 才有意义
- 使用 vector 模拟 map 时，vector 保存元素类型为 pair，注意不能把 pair 的 first 的类型声明为const，因为对 vector 排序时，它的元素将会通过赋值移动
- 还需自己为 pair 写一个自定义的比较函数，只需要使用 pair 的 key
- 同时还需要第二个比较函数来进行查找，用于排序的函数接受的参数是两个 pair 对象，而用于查找的函数接受的参数是 pair 的key，还有一个 pair 
- 还有一个附加的麻烦，你不会知道 key 还是 pair 是作为第一个实参传递的，所以需要两个函数，一个 key 在前，一个 pair 在前。（当用binary\_search算法时，这个pair用于遍历 vector 的元素）
- 因为定义的类有三个重载的 `operator()` 作为比较函数，所以会导致函数对象无法适配

## 24. 当关乎效率时应该在 map::operator[] 和 map-insert 之间仔细选择
- 更新元素的话，用 `operator[]` 更好
- 插入新元素的话，用 insert 更好; operator \[\] 会先构造一个默认的 value 类型，再为其赋值

## 25. 熟悉散列容器
- 如果关键字类型固有就是无序的，或者性能测试发现可以用哈希技术解决，就可以使用无序容器。
- 理论上哈希技术能获得更好的平均性能，但在实际中想要达到很好的效果还需要进行一些性能测试和调优工作。
- 无序容器在存储上组织为一组桶。还提供了一组管理桶的函数。

## 26. 尽量用 iterator 代替 const iterator
- insert 和 erase 的一些版本要求 interator。
- 不可能把 const\_iterator 隐式转换成 iterator，见条款27
- 从 reverse\_iterator 转换而来的 iterator 在转换之后可能需要调整。见条款28

## 27. 用 distance 和 advance 把 const_iterator 转化成 iterator
- 首先，const\_cast 并不好使。但是对于 vector 和 string 容器的迭代器使用 const\_cast 也许能通过编译，这是因为 vector 和 string 的有些实现是用的真实的指针作为迭代器的。
```
typedef deque<int> IntDeque;
typedef IntDeque::iterator Iter;
typedef IntDeque::const_iterator CIter;
IntDeque d;
CIter ci;
...
Iter i( d.begin() );
advance(i, distance<CIter> (i, ci) );//把 i 移到指向 ci 位置
```

- `advance` 是一个模板函数，代码中显示地调用类型为 CIter 的实例，如果让编译器自己推测的话，根据第一个类型为 Iter 编译器会去调用类型为 Iter 的函数实例，会报出一串冗长的错误。  
- `advance` 与 `distance` 都在头文件 < iterator > 中。distance 返回指向同一个容器的两个迭代器之间的距离，advance 则用于将一个 iterator 移动指定的距离。  
- 这样做的效率如何？取决于迭代器类型。对于随机访问迭代器，这是常数时间的操作；对于双向迭代器（也就是包括散列容器的一些实现在内的一些容器），这是线性时间的操作。因为需要线性时间来生成一个和 const\_iterator 等价的 iterator。

## 28. 了解如何通过 reverse_iterator 的 base 得到 iterator
- 对于插入操作而言，reverse\_iterator 的 base 得到的 iterator 能在正确位置插入
- 对于删除操作而言，直接对 reserve\_iterator 调用 base 得到的 iterator 会删除 iterator 前方的元素，即 reverse\_iterator 后方的元素。所以应该使用`v.erase( (++ri).base() )`，这样会准确删除 reverse\_iterator 指向的数据

## 29. 需要一个一个字符输入时考虑 istreambuf_iterator
```
ifstream inputFile( "data.txt" );
//它不正确，请看下文。并看此处与条款6的区别
string fileData( (istream_iterator<char>(inputFile)), istream_iterator<char>() );
``` 
1. 它忽略了空格。可以调用 `inputFile.unset(ios::skipws)` 来取消对空格的忽略
2. istream\_iterator 所依靠的 operator>> 函数进行的是格式化输入，这意味这每次你调用的时候它们都必须做大量工作。它们必须建立和销毁岗哨（sentry）对象（为每个operator>>调用进行建立和清楚活动的特殊 iostream 对象），它们必须检查可能影响它们行为的流标志（比如skipws），它们必须进行全面的读取错误检查，而且如果它们遇到问题，它们必须检查流的异常掩码来决定是否该抛出异常。如果进行格式化输入，那些都是重要的活动，但如果你需要的只是从输入流中抓取下一个字符，那就过度了。

- 更有效的方法是使用 STL 最好的秘密武器之一: `istreambuf_iterator`。你可以像 istream\_iterator 一样使用 istreambuf\_iterator，但 istream\_iterator< char > 对象使用 operator>> 从输入流读取单个字符，但 istreambuf\_iterator< char > 对象进入流的缓冲区并直接读取下一个字符。（更明确的说，一个 istreambuf\_iterator< char >对象从一个 istream is 中读取会调用 `is.rdbuf()->sgetc()` 来读取 is 的下一个字符。）
- 使用 ostreambuf\_iterator 用于相应的无格式一个一个字符输出的动作。它们没有了 ostream\_iterator 的开销和灵活性，所以它们也做得更好。

## 30. 确保目标空间足够大
```
int transmogrify(int x);// 这个函数从x产生一些新值
vector<int> values;
...// 把数据放入values
vector<int> results;
//下一句会毫无疑问的崩溃
//把transmogrify应用于values中的每个对象，再把返回的值附加到results
transform(values.begin(), values.end(), results.end(), transmogrify);
//正确用法，使用back_inserter
transform(values.begin(), values.end(), back_inserter(results), transmogrify);
```

- back\_inserter 返回的迭代器会调用 push\_back，所以可以再任何提供了 push\_back 的容器上使用 back\_inserter。front\_inserter 可以用在任何提供了 push\_front 的容器上，当然 front\_inserter 会颠倒元素顺序

```
//用 inserter 在中间插入
transform(values.begin(), values.end(), inserter(results, results.begin()+results.size()/2), transmogrify);
```

- 使用 transform 每次只写入一个值，你无法改变。这对于连续内存容器( vector、string 和 deque ）来说这可能很昂贵。
- 另外有些时候必须使用 resize 来确保空间；有时用 reserve 配合 inserter 来减少内存分配次数

## 31. 了解你的排序选择
- 很多程序员想到排序对象时，只有一个算法出现在脑海: `sort`。（有些程序员想到 qsort，但一旦他们看到条款46，他们会放弃 qsort 的想法并用 sort 的想法取代之。
- sort 是一个令人称赞的算法，但如果你不需要你就没有必要浪费表情。有时候你不需要完全排序
- 有一个算法就 partial\_sort，它能准确地完成它的名字所透漏的事情:
```
bool qualityCompare( const Widget& lhs, const Widget& rhs)
{
...//返回 lhs 的质量是不是比 rhs 的质量好
}
//严格区分前20个
partial_sort( widgets.begin(), widgets.begin()+20, widgets.end(), qualityCompare);//把最好的前20个元素按顺序放在 widgets 的前端，第1个是最好的，然后第20个是第20好的
//并不仔细区分前20个
nth_element( widgets.begin(), widgets.begin()+19, widgets.end(), qualityCompare);//前20个是最好的，但这20个里并不知道哪个是最好，那个是第20好
```

- `partial_sort` 和 `nth_element` 以它们喜欢的方式排序值等价的元素，而且你不能控制它们在这方面行为
- 对于完整的排序，可以选择不同的算法来进行排序。有些算法是稳定的（插入、归并等），稳定的算法对于那些等价的值，它们的相对位置在排序后不改变；有些是不稳定的。有一个专门的 stable\_sort 算法
- partion 算法接受一个一元谓词，将满足这个谓词的元素移动到前端，并返回不满足这个条件的第一个迭代器
- partion 和 stable\_partion 只需要双向迭代器，而 sort、stable\_sort、paitial\_sort 和 nth\_element 都需要随机迭代器
- 使用 list::sort 代替 sort 和 stable\_sort，但如果 list 需要 partial\_sort 或 nth\_element 的效果，必须间接完成这个任务:
	- 比如先把元素拷贝到一个支持随机访问迭代器的容器中
	- 或者建立一个 list::iterator 的容器，对那个容器使用算法，然后通过迭代器访问 list 元素
	- 或者使用有序的迭代器容器的信息来迭代地把 list 的元素结合到你想让它们所处的位置

- 这些算法的性能排序，需要更少资源（时间和空间）的算法列在了前面  
1.partition  
2.stable\_partition  
3.nth\_element  
4.partial\_sort  
5.sort  
6.stable\_sort  

- 如果你选择的算法只完成了你需要的（比如用 partion 代替完全排序），你能得到的不仅是可以最清楚地表达了你要做的代码，而且是使用 STL 最高效的方法来完成它。

## 32. 如果你真的想删除东西的话就在类似remove的算法后接上erase
```
template<class ForwardIterator, class T>
ForwardIterator remove( FrowardIterator first, ForwardIterator last, const T& value);
```

- 它不是成员函数，只接受迭代器，因而没有办法删除容器的元素
- 它只是把给定区间中所有“不删除的”元素移到开头；并返回一个迭代器，这个迭代器指向第一个需要删除的元素
- 并不保证后面的元素的值。对于`vector<int>::iterator newEnd( remove(v.begin(), v.end(), 99) )`如果“不删除的”元素在 v.begin()和 newEnd 之间，“删除的”元素就必须在 newEnd 和 v.end() 之间——这好像很合理。但事实并非如此。“删除的”值完全不必存在于v中了。remove并没有改变区间中的元素顺序，所以不会把“删除的”元素放在结尾。虽然标准没有要求，但大部分实现中 newEnd 后的元素仍保持着它们的原值
- 真的想删除，应该`v.erase( remove(v.begin(), v.end(), 99), v.end() ); `
- `remove_if` 和 `remove` 很相似，`unique` 的行为也像 `remove`。
- 但 `list::remove` 真的删除东西，而且比 erase-remove 高效的多，`list::unique` 也真的删除邻近的重复值，也比 erase-unique 高效

## 33. 提防在指针的容器上使用类似remove的算法
- 如条款32所说，remove 调用之后指针就已经被覆盖掉了。被覆盖掉的指针指向的内存无法被释放了
- 很多情况下，`partion` 是个合理的选择
- 如果使用智能指针，直接使用 erase-remove 是非常棒的
- 不管你怎样选择处理动态分配指针的容器，通过智能指针、在调用 remove 类似算法以前手动删除并废弃（置为nullptr）或者一些你自己发明的技术，本条款的指导意义依然一样：提防在指针的容器上使用类似 remove 的算法

## 34. 注意哪个算法需要有序空间
- remove 需要前向迭代器和可以通过这些迭代器赋值的能力。所以不能用于输入迭代器划分的区间，也不能是 map 和 multimap，也不能是 set 和 multiset 的一些实现（条款22）
- 很多排序算法（条款31）需要随机访问迭代器，所以不可能在 list 上调用
- 如果你冒犯了这些规则，你的代码将不能编译，可能会出现一个非常冗长和不可理解的错误（条款49）。但其它算法的需求则更为狡猾。在这些之中，可能最常见的就是一些算法需要有序值的空间。
- 既可以和有序又可以和无序区间合作的算法很少，但档操作有序区间的时候它们最有用。
- 操作有序数据的算法的表

|算法|
|:--:|
|binary\_search|lower\_bound
|upper\_bound|equal\_range|
|set\_union|set\_intersection
|set\_difference|set\_symmetric\_difference
|merge|inplace\_merge
|includes|

- 下面的算法一般用于有序空间，虽然它们不要求：`uniqe` 和 `unique_copy`

#### 为什么需要有序空间
- 搜索算法 binary\_search、lower\_bound、upper\_bound 和 equal\_range 需要有序空间，因为它们使用二分法查找值。像 C 库中的 bsearch，这些算法保证了对数时间的查找，但作为交换，你必须给它们已经排序过的值
- 事实上，这些算法保证对数时间查找不是很正确。仅当传给它们的是随机访问迭代器时它们猜保证对数时间。如果给双向迭代器，它们仍进行对数次比较，但需要线性时间
- 算法 set\_union、set\_setintersection、set\_difference 和 set\_difference 提供了线性时间。为什么需要有序空间？如果不是的话，它们不能以线性时间完成它们的工作。你开始发现一个趋势——需要有序区间的算法为了比它们用于可能无需区间提供更好的性能而这么做，你是对的。
- merge 和 inplace\_merge 执行了有效的单遍合并排序算法：读取两个有序空间。产生一个新有序区间。它们以线性时间执行，如果它们不知道源区间已经有序就不能完成
- 最后一个需要有序空间的算法是 includes。它用来检测是否一个区间的所有对象也在另一个区间中。因为 includes 假设两个区间的都有序，所以它保证了线性时间性能。没有那个保证，一般来说它会变慢。

#### unique
- unqie 和 unique\_copy 在无序空间上也提供了定义良好的行为。标准库的描述如下：
> 从每个相等元素的*连续组*中去除第一个以外所有的元素

- 换句话说，它并不去除两个不相邻的重复元素
- 此外它和 remove 一样，只是区分出不除去的元素（条款32）

#### 排序方式
- STL 允许你指定排序的比较函数
- 需要保证算法需要的行为与排序的方式一致

```cpp
//会导致未定义的结果
vector<int> v;
...
sort( v.begin(), v.end(), greater<int>() );//降序排列
...
bool flag = binary_search( v.begin(), v.end(), 5 );//假设它是升序排列！！
```
默认情况下，binary\_search 假设它搜索的区间是以小于号“&lt;”排列，也就是升序。所以上面代码会导致未定义的结果
```cpp
bool flag = binary_search( v.begin(), v.end(), 5, greater<int>() );//把greater作为比较函数
```

所有需要有序空间的算法（也就是除了 unique 和 unique\_copy 外本条款的所有算法）通过等价来判断是否“相同”，就像标准关联容器。相反，unique 和 unique\_copy 判断的默认方式是通过相等，但是你可以通过传给这些算法一个定义了“相同”意义的判断式来覆盖这个默认情况。

#### 总结
11个需要有序空间的算法为了提供更好的性能而这么做。只要你记住只传给它们有序区间，只要你保证用于算法的比较函数和用于排序的一致，你就会酷爱没有麻烦的查找、设置和合并操作，加上你会发现 unique 和 unique\_copy 除去了所有的重复值，正如你要它们完成的一样

## 35. 通过 mismatch 或 lexicographical 比较实现简单的忽略大小写字符串比较
#### mismatch
```
//单个字符的忽略大小写比较函数
int ciCharCompare( char c1, char c2)
{
	int ic1 = tolower( static_cast<unsigned char>(c1) );
    int ic2 = tolower( static_cast<unsigned char>(c2) );
    if( ic1 < ic2 )return -1;
    if( ic1 > ic2 )return 1;
    return 0;
}
int ciStringCompareImpl( const string& s1, const string$ s2)
{
	typedef pair<string::const_iterator, string::const_iterator> PSCI;//PSCI = "pair of string::c_iter"
    //mismatch会返回一对迭代器
    PSCI p = mismatch( s1.begin(), s1.end(), s2.begin(), not2( ptr_fun(ciCharCompare) ) );
    if( p.first == s1.end() )
    {
    	if( p.second == s2.end() ) return 0;
        else return -1;
    }
    else
    	return ciCharCompare( *p.first, *p.second);//不相同的字符的比较结果就是字符串的比较结果
}
//这个是接口
int ciStringCompare( const string& s1, const string& s2)
{
	if( s1.size() <= s2.size() )
    	return ciStringCompareImpl( s1, s2 );
    else
    	return ciStringCompareImpl( s2, s1 );
}
```
#### lexicographical_compare
lexicographical\_compare 是 strcmp 的泛型版本。strcmp 只对字符数组起作用，但lexicographical\_compare 对所有任何类型的值的区间都起作用。同时，strcmp 总是比较两个字符来查看它们的关系是相等、小于或大于。lexicographical\_compare 可以传入一个二元谓词
```
bool ciCharLess( char c1, char c2 )
{
	return toLower(static_cast<unsigned char>(c1)) < toLower(static_cast<unsigned char>(c2));
}
bool ciStringCompare( const string& s1, const stirng& s2)
{
	return lexicographical_compare( s1.begin(), s1.end(), s2.begin(), s2.end(), ciCharLess);
}
```
#### 非标准C库
跟本条款其它函数一样没有提供国际化的支持  
被优化为只做一件事情，对长字符串运行起来一般比通用算法 mismatch 和 lexicographical\_compare 块的多。如果那对你很重要，那你可能不在乎你用非标准C函数完成标准STL算法。有时候最有效地使用STL的方法是认识到其它方法更好。
```
int ciStringCompare(const string& s1, const string& s2)
{
return strcasecmp(s1.c_str(), s2.c_str());// 你的系统上的
}
```

## 36. 了解 copy_if 的正确实现
STL有11个名字带 copy 的算法  

|算法
|:--:|
|copy|copy\_backward
|replace\_copy|reverse\_copy
|replace\_copy\_if|unique\_copy
|remove\_copy|rotate\_copy
|remove\_copy\_if|partioal\_sort\_copy
|unintialized\_copy

但没有一个是 copy\_if。如果你只是简单地想拷贝一个区间中满足某个判断式的元素，你只能自己做
#### 正确实现
```
//正确实现
template<typename InputIterator, typename OutputIterator, type Predicate>
OutputIterator copy_if( InputIterator begin, InputIterator end, OutputIterator destBegin, Predicate p)
{
	while( begin != end )
    {
    	if( p(*begin) )
        	*destBegin++ = *begin;
        ++begin;
    }
}
```

#### 历史版本
STL曾经有过一个copy\_if，实现如下，但它已经是历史了
```
template<typename InputIterator, typename OutputIterator, type Predicate>
OutputIterator copy_if( InputIterator begin, InputIterator end, OutputIterator destBegin, Predicate p)
{
	return remove_copy_if( begin. end. destBegin, not1(p) );
}
```
代码中 `not1` 不能直接用于函数指针，函数指针必须先传给ptr\_fun。要调用这个 copy\_if ，你传递的不仅是一个函数对象，而且是一个***可适配***的函数对象。但标准STL算法从来不要求它们的仿函数是可适配的，copy\_if也不该要求。上面的实现是好的，但不够好

## 37. 用 accumulate 或 for_each 来统计区间
- count 和 count\_if 判断多少元素满足表达式
- min\_element 和 max\_element 获得最大和最小值
- 但有时，你需要一些自定义的方式统计（summarize）区间，需要比 count、conut\_if、min\_element 或 max\_element 更灵活的东西。
- STL 为你准备了算法，它叫做 accumulate。它不存在与`<algorithm>`，而是和其它三个“数值算法”一样都在`<numeric>`中。其它三个是 inner\_product、adjacent\_difference 和 partial\_sum

#### accumulate
```
list<double> ld;// 建立一个list<double>
...
//初始化的值必须是0.0而不是0！！
double sum = accumulate(ld.begin(), Id.end(), 0.0);
```
- 进行数值计算是默认行为。可以传入一个二元谓词。
```
string::size_type stringLengthSum(string::size_type sumSoFar, const string& s)
{
	return sumSoFar + s.size();
}
set<string> ss;
...
//初值为设0
size_t lengthSum = accumulate( ss.begin(), ss.end(), 0, stringLengthSum);
```

- 只要求输入迭代器，所以甚至可以使用 istream\_iterator 和 istreambuf\_iterator
- 计算数值区间的积甚至更简单，可以使用标准 multipies 仿函数类
```
vector<float> vf;
...
//初始值设置1.0f
float product = accumulate( vf.begin(), vf.end(), 1.0f, multipies<float>() );
```

#### 不能使用 accumulate 的地方
```
struct Point
{
Point(double initX, double initY): x(initX), y(initY) {}
double x, y;
};
class PointAverage: public binary_function<Point, Point, Point>//为什么继承参见条款40
{
public:
	PointAverage(): numPoints(0), xSum(0), ySum(0) {}
	const Point operator()(const Point& avgSoFar, const Point& p)
    {
	++numPoints;
	xSum += p.x;
	ySum += p.y;
	return Point(xSum/numPoints, ySum/numPoints);
}
private:
	size_t numPoints;
	double xSum;
	double ySum;
};
//使用的实例
list<Point> lp;
...
Point avg = accumulate(lp.begin(), lp.end(), Point(0, 0), PointAverage());
```

- 标准库禁止传给 accumulate 的函数中有什么副作用。成员变量 numPoints、xSum 和 ySum 的修改造成了一个副作用
- 技术上讲，上述代码会导致结果未定义。实际上，很难想象它无法工作。

#### 使用 for_each
- 没有 accumulate 那么多限制
- 只接收一个实参（当前的区间元素），而且当完成时返回一个函数对象，所以需要给这个使用的仿函数类添加一个成员函数来获取我们需要的统计信息。
- 值得注意的是，传给 for\_each 的函数可能有副作用
- for\_each 用来统计一个区间是合法的，但是没有 accumulate 清楚

```
struct Point {...);// 同上
class PointAverage: public unary_function<Point, void> 
{
public:
	PointAverage(): xSum(0), ySum(0), numPoints(0) {}
	void operator()(const Point& p)
	{
	++numPoints;
	xSum += p.x;
	ySum += p.y;
	}
	Point result() const
	{
	return Point(xSum/numPoints, ySum/numPoints);
	}
private:
	size_t numPoints;
	double xSum;
	double ySum;
};
list<Point> Ip;
...
Point avg = for_each(lp.begin(), lp.end(), PointAverage()).result;
```

#### 总结
> 就个人来说,我更喜欢用accumulate来统计,因为我认为它最清楚地表达了正在做什么,但是for_each也可以,而且不像accumulate,副作用的问题并不跟随for_each。两个算法都能用来统计区间使用最适合你的那个。
> 你可能想知道为什么for_each的函数参数允许有副作用, 而accumulate不允许。这是一个刺向STL心脏的探针问题。唉,尊敬的读者,有一些秘密总是在我们的知识范围之外。为什么accumulate和for_each之间有差别?我尚待听到一个令人信服的解释。


## 38. 把仿函数类设计为用于值传递
- STL 用户的函数对象必须按值传递。如果用引用传递，有些 STL 算法甚至不能编译
- 你的任务就是确保按值传递时你的函数对象行为良好
    - 第一：你的函数对象应该很小，否则它们的拷贝会很昂贵
    - 第二：你的函数对象必须是非多态的，它们不能使用虚函数


- 函数对象比真的函数优越的原因之一是仿函数可以包含你需要的所有状态。有写函数对象自然会很中，保持这样的仿函数给 STL 算法和传它们的函数版本一样容易是很重要的

#### 如何使用多态
- 但是禁止多态的仿函数是不切实际的。C++支持继承层次和动态绑定，这些特性很有用。  
- 建立一个包含一个指向实现类的指针的小而单态的类，然后把所有数据和虚函数放到实现类: 
- 以下代码忽略了很多细节，可见《Effective C++》条款34，在Gamma等的《设计模式》中这叫“Bridge模式”。Sutter在《Exceptional C++》中称之为“Pimpl惯用法”
```
template<typename T>
class BPFCImpl : public unary_function<T, void> {
private:
	Widget w;
	int x;
	virtual ~BPFCImpl();// 多态类需要虚析构函数
	virtual void operator()(const T& val) const;
	friend class BPFC<T>;// 让BPFC可以访问这些数据
};
// 小的,单态版的BPFC
template<typename T>
class BPFC : public unary_function<T, void>
{
private:
	BPFCImpl<T> *pImpl;// 这是BPFC唯一的数据
public:
	void operator()(const T& val) const// 现在非虚;
	{
		pImpl->operator() (val);// 调用BPFCImpl的
	}
};
```

## 39. 用纯函数做判断式
- 纯函数: 如果一个函数在不同时间用同样的参数调用可能导致不同的结果，那就与纯函数的定义相反
- 因为 STL 都是按值传递，所以不会改变函数对象的任何成员变量
- 不管你怎么写你的判断式，它们都应该是纯函数，不要对相同参数残生不同结果

## 40. 使仿函数类可适配
- 四个标准函数适配器（not1、not2、bing1st 和 bind2nd）都需要存在某些 typedef，一些非标准 STL 兼容的适配器（比如SGI和Boost的）也需要。提供了这些 typedef 的函数对象称为*可适配的*，而缺乏 typedef 的函数对象则不可适配
- 四个typedef: `argument_type、first_argument_type、second_argument_type 和 result_type`
- 不同类型的仿函数需要提供这些名字的不同子集
- 你不需要知道任何关于这些 typedef 的事情，那是因为提供它们的正规方法是从一个基类，或，更精确的说，一个基结构，继承它们
- operator() 带有一个实参的仿函数类，要继承的是 std::unary\_function。operator() 带有两个实参的仿函数类，要继承的是 std::binary\_function
- 但两个基结构都是模板，不能直接继承

```
template<typename T>
class MeetsThreshold: public std::unary_function<Widget, bool>
{
private:
	const T threshold;
public:
	MeetsThreshold(const T& threshold);
	bool operator()(const Widget&) const;
	...
};
struct WidgetNameCompare: public std::binary_function<Widget, Widget, bool>
{
	bool operator()(const Widget& lhs, const Widget& rhs) const;
};
```

- 两种情况下，注意传给 unary\_function 或 binary\_function 的类型声明与 operator() 的参数类型和返回值类型一样
- 代码中一个是类，一个是结构，类中有私有成员。而对于那些不需要私有成员的仿函数，声明为类和结构纯粹是个人风格问题
- 另外，虽然 operator() 的参数都是 const Widget& ，但传给 binary\_function 的是 Widget。一般来说，传给 unary\_function 和 binary\_function 的非指针类型都去掉了 const 和引用。（不要问为什么，理由不很好也不很又去。如果你真的想知道，写一些没有去掉它们的程序，然后去解剖编译器诊断结果。如果完成了这步，你仍然对这个问题感兴趣，访问 boost.org 然后看看他们关于特性和函数对象适配器的工作）
- 对于指针参数，则传给 binary\_function 的类型和 operator() 的类型一样

```
struct PtrWidgetNameCompaer: public std::binary_function<const Widget*, const Widget*, book>
{
    bool operator()(const Widget* lhs, const Widget* rhs)const;
};
```

- STL 模仿了C++函数，假设每个仿函数类都只有一个 operator() 函数，而且这个函数的参数和返回类型与传给 unary\_function 或 binary\_function 的一致。这意味着，如果你让一个结构含有两个 operator() 函数，那这个仿函数可能可以和最多一种它的调用形式适配(你传参数给 binary\_function 的那个)，而只能一半适配的仿函数可能只比完全不能适配要好
- 有时候有必要给一个仿函数类多个调用形式（因此得放弃可适配行），条款7、20、23和25给了这种情况的例子。但这种仿函数类是例外，不是规则。可适配性是重要的，每次你写仿函数类时都应该努力促进它


## 41.了解使用 ptr_fun、mem_fun 和 mem_fun_ref 的原因
- 它们做了很重要的工作，其主要任务之一是覆盖C++固有的语法矛盾之一
- 函数调用共有三种形式: 1.非成员函数f(x)，2.成员函数x.f()，3.指针调用成员函数x->f()
- 但STL里有一个普遍习惯：函数和函数对象总使用用于非成员函数的语法形式调用。即STL算法（包括for\_each）牢牢地绑定在了第一种调用形式上
- mem\_fun
```
template<typename R, typename C>
mem_fun_t<R, C>
mem_fun( R(C::*pmf)() );//C是类，R是返回值
...
list<Widget*> lpw;
...
//不加mem_fun的话就无法编译
for_each( lpw.begin(), lpw.end(), mem_fun(&Widget::test) );//ok
```


- ptr\_fun，使普通的函数适配成仿函数(functor),对普通的函数加上了某些typedef，见条款40。这些typedef在用于算法时是不需要的，但在用于 bind1st 和 bind2nd 时是必须的
- 如果你关于什么时候使用 ptr\_fun 什么时候不使用而感到困惑，那就考虑每当你传递一个函数给STL组件时都使用它。STL将不在乎，并且没有运行期的惩罚。可能出现的最坏的情况就是一些读你代码的人当看见不必要的 ptr\_fun 使用时，可能会扬起眉毛
- 一个与 ptr\_fun 有关的可选策略是只有当你被迫时才使用它。如果当typedef是必要而你忽略了它时，你的编译器将退回你的代码。然后你得返回去添加它
- 而 mem\_fun 和 mem\_fun\_ref 的情况则完全不同。只要你传一个成员函数给STL组件，你就必须使用它们，因为除了增加typedef（可能是或可能不是必须的）之外，它们把调用语法从一个通常用于成员函数的适配到在STL中到处使用的。传递成员函数指针而不使用它们，你得代码将无法编译
- 容器中是指针时，使用 mem\_fun，是普通对象而不是指针时，使用 mem\_fun\_ref。因为是先发明的指针的版本，然后才有了普通对象的版本，所以名字很不优雅


## 42.确定less< T >表示operator<
- 通常，师徒修改std里的组件确实是禁止的，但是在一些情况下，修补是允许的。具体来说，程序员被允许用自定义类型特化std内的模板。特化std模板的选择几乎总是更为优先，但很少发生，这确实合理。例如，只能指针类的作者经常想让他们的类在排序的时候行为表现的像内建指针，因此用于智能指针类型的std::less特化并不罕见
- 让 less 做做除 `operator<` 以外的事情是对程序员预期的无故破坏。`operat<` 不仅是 less 的默认方式，它还是程序员希望 less 做的
```
multiset<Widget> widgets;//表示使用less<Widget>进行排序，实际上每个人都会假设它是按operator< 来排序，如果你特化了less<Widget>，那你实际上就骗到了其它人
```


- 在STL中没有一个用到 less 的地方你不能指定一个不同的比较类型
- 如果你使用less，保证它表示`operator<`。如果你想使用一些其它标准排序对象，建立一个特殊的*不叫做less*的仿函数类。它真的很简单


## 43.尽量用算法调用代替手写循环
- 每个算法接受至少一对用来指示将被操作的对象区间的迭代器。比如，min\_element 可以找出区间中的最小值，而 accumulate 则对区间元素做求和运算（条款37），partition 将区间内的元素分割为满足和不满足某判决条件的两部分（条款31）
- 对许多C++程序员而言，使用循环比调用算法的想法自然多了，并且读懂循环比弄得 mem\_fun\_ref 的意义和取成员函数的地址要舒服多了，但是，调用算法比手写的循环更优越: 
    1. 效率，算法比手写循环更高效
    2. 正确性，手写循环更易出错
    3. 可维护性，算法更干净、更直观


- 效率  
库的实现者可以利用它们知道的容器的具体实现的优势。比如，deque的对象通常存储在（内部的）一个或多个固定大小数组上，基于指针的遍历比基于迭代器的遍历更快，但只有库的实现者可以使用基于指针的遍历；一些STL容器和算法的实现版本将 deque 的内部数据结构引入了 account，这样的实现比“通常的”快20%  
此外，除了最微不足道的STL算法，所有的STL算法使用的计算机科学都比一般的C++程序员能拿得出来的算法复杂——有时会复杂得多。几乎不可能被打败的 sort 及其同族算法（比如stable\_sort(), nth\_element()等，见条款31）;适用于有序空间的搜索算法（比如binary\_search，lower\_bound等，见条款34和35）也一样好；就算是很平凡的任务，比如从连续内存容器中除去某些对象，使用 erase-remove 惯用法都比绝大多数程序员手写的循环更高效
- 正确性  
手写循环时麻烦的地方在于确保所使用的迭代器有效，并且指向你所期望的地方
- 可维护性  
算法的名字就传达了大量的语义信息，这使得它们比循环清晰多了
- 关于代码清晰度的底线是:这完全取决于你想在循环里做的是什么。如果你要做的是算法已经提供了的，或是非常接近于它提供的，调用泛型算法更清晰。如果循环里要做的事非常简单，但调用算法时却需要使用绑定和适配器或需要独立的仿函数类，你恐怕还是写循环比较好

## 44.尽量使用成员函数代替同名的算法
- 关联容器提供了 count、find、lower\_bound、upper\_bound 和 equal\_range
- list 提供了 remove、remove\_if、unique、sort、merge 和 reverse
- 成员函数更快，其次，它们与容器结合得更好（尤其是关联容器）
- 另外，正如条款19所说，STL算法判断两个对象是否相同的方法是检查它们是否相等，而关联容器是用等价来测试它们的“同一性”。因此， find 算法搜索用到是相等，而关联容器的 find 成员函数用的是等价


## 45.注意 count、find、binary_search、lower_bound、upper_bound 和 equal_range 的区别
#### 标准序列容器
- 如果是无序空间，就只能使用线性时间的 count、count\_if、find 和 find\_if，且这几个算法都用的相等的概念
- 如果是有序空间，可以通过 binary\_search、lower\_bound、upper\_bound 和 equal\_range 来进行对数时间的搜索，同时它们使用的是等价的概念
- binary\_search :“它在吗？”
- lower\_bound :“它在吗？如果是，第一个拷贝在哪里？如果不是，它将在哪里？”。对于它返回的结果要进行非end判断和是否与所寻值**等价**的判断
- equal\_range :“它在吗，如果是，它在哪？”，返回了一对迭代器。如果两个相同，就意味着没找到；不相同的话可以用 distance 获得数量

#### 关联容器
- 提供了除 binary\_search 外同名的成员函数
- binary\_search 可以用 count 和 find 代替，在 mulitset 和 multimap 上 find 优于 count，因为 count 要遍历整个容器；对于 map 和 set 这就是一个不问题了


## 46.考虑使用函数对象代替函数作为算法的参数
- 一般而言，高级编程语言的抽象层次越高，产生的代码效率就越低。但STL函数对象传给算法的代码效率比传递普通函数的效率高
- 因为对于普通函数，传递的是函数指针，每次都要调用这个函数指针，即使函数是内联函数；而对于函数对象，如果 operator() 是内联的，那它就真的是内联的
- 所以，在速度上，C++的 sort 实际上总是使 C 的 qsort 感到窘迫。当然，C++有函数、实例化的类模板和operator()函数需要调用，而C只是进行简单的函数调用，但所有的C++开销都在**编译期**被吸收
- 另外一个原因是让代码通过编译。有些STL平台完全拒绝了有效代码，因为其在处理 const 成员函数时可能有bug。而使用函数对象则无问题
- 另外一个细微原因是让你避免细微的语言陷阱。比如，一个函数模板实例化的名字不一定等价于函数名；函数对象则无此问题
- 函数非常有用，但涉及有效的STL编程时，函数对象经常更有用

## 47.避免产生只写代码
- 注意可读性质  
去掉一个 vector 中小于 x，而出现在至少和 y 一样大的最后一个元素之后的所有元素
```cpp
vector<int> v;
int x, y;
...
v.erase(remove_if(
	find_if(v.rbegin(),v.rend(),
    	bind2nd(greater_equal<int>(), y)).base(),
    v.end(),
	bind2nd(less<int>(), x)),
	v.end());
```

更可读的一种写法

```cpp
typedef vector<int>::iterator VecIntIter;
//把rangeBegin初始化为指向最后一个
//出现一个大于等于y的值后面的元素。
//如果没有那样的值,
//把rangeBegin初始化为v.begin()。如果那个值的
//最后一次出现是v中的最后一个元素,
//就把rangeBegin初始化为v.end()
VecIntIter rangeBegin = find_if(v.rbegin(), v.rend(), bind2nd(greater_equal<int>(), y)).base();
//从rangeBegin到v.end(),删除所有小于x的值
v.erase(remove_if(rangeBegin, v.end(), bind2nd(less<int>(), x)), v.end());
```

一个先寻找，再删除的伪代码，这中成为“只写（write-only）代码，因为写起来容易，读起来难”
```
v.erase(remove_if(find_if(v.rbegin(), v.rend(), something).base(),
	v.end(),
	something)),
	v.end());
```


## 48.总是 include 适当的头文件
- 不同的STL平台上的头文件可能包含了不同文件，有的 `vector` 头文件包含了 `string` 头文件
- 如果无法移植，请别指责你将要移植的编译器或库实现。如果你缺少了需要的头文件，这就是你的过错。无论何时你引用了 std 名字空间里的元素，你就应该对 #include 何时的头文件负责
- 几乎所有的容器都在其同名的文件里。例外的是，头文件`set`包含了 set 和 multiset，头文件`map`包含了 map 和 multimap
- 除了四个算法以外，所有算法都在 `algorithm` 中声明。例外的是 accumulate（条款37）、inner\_product、adjacent\_difference 和 partial\_sum，这些算法在头文件 `numeric` 中声明
- 特殊的迭代器，包括 istream\_iterators 和 istreambuf\_iterators（条款29），在头文件 `iterator` 中声明
- 标准仿函数（比如less）和仿函数适配器（比如not1、bind2nd）在头文件 `functional` 中声明


## 49.学习破解有关STL编译器的诊断信息
有经验的STL程序员发展出一项类似的技能。他们可以不假思索地在内部把比如 `std::basic_string<char, struct std::char_traits<char>, class std::allocator<char> >` 翻译为string。你也要发展这项技能,但在你能做到之前,记得你总是可以通过用更短的记忆术替换冗长的基于模板的类型名字来把编译器诊断信息降低到可以理解的东西。在许多场合,你要做的就是用你已经使用的typedef名字替换typedef展开。  
这里有一些应该能帮助你理解有关STL的编译器消息的其它提示:

- 对于vector和string,迭代器有时是指针,所以如果你用迭代器犯了错误,编译器诊断信息可能会提及涉及指针类型。例如,如果你的源代码涉及`vector<double>::iterator`,编译器消息有时会提及double\*指针。(一个值得注意的例外是当你使用来自STLport的STL实现,而且你运行在调试模式。那样的话,vector和string的迭代器干脆不是指针。对STLport和它调试模式的更多信息,转向条款50。)
- 提到back\_insert\_iterator、front\_insert\_iterator或insert\_iterator的消息经常意味着你错误调用了back\_inserter、front\_inserter或inserter,一一对应,(back\_inserter返回back\_insert\_iterator类型的对象,front\_inserter返回front\_insert\_iterator类型的对象,而inserter返回insert\_iterator类型的对象。关于使用这些inserter的信息,参考条款30。)如果你没有调用这些函数,你(直接或间接)调用的一些函数做了。
- 类似地,如果你得到的一条消息提及binder1st或binder2nd,你或许错误地使用了bind1st或bind2nd。(bind1st返回binder1st类型的对象,而bind2nd返回binder2nd类型的对象。)
- 输出迭代器(例如ostream\_iterator、ostreambuf\_iterators(参见条款29),和从back\_inserter、front\_inserter和inserter返回的迭代器)在赋值操作符内部做输出或插入工作,所以如果你错误使用了这些迭代器类型之一,你很可能得到一条消息,抱怨在你从未听说过的一个赋值操作符里的某个东西。为了明白我的意思,试着编译这段代码:
```
vector<string*> v;
// 试图打印一个
copy(v.begin(), v.end(), ostream_iterator<string>(cout, "\n")); // string*指针的容器,被当作string对象
```

- 你得到一条源于STL算法实现内部的错误信息(即,源代码引发的错误在 algorithm 中),也许是你试图给那算法用的类型出错了。例如,你可能传了错误种类的迭代器。要看看这样的用法错误是怎样报告的,通过把这段代码喂给你的编译器来启发(并愉快!)自己:
```
list<int>::iterator i1, i2;
// 把双向迭代器传给一个需要随机访问迭代器的算法
sort(i1, i2);
```

- 你使用常见的STL组件比如vector、string或for\_each算法,而编译器说不知道你在说什么,你也许没有#include一个需要的头文件。正如条款48的解释,这问题会降临在长期以来都可以顺利编译而刚移植到新平台的代码

## 50.让自己熟悉有关STL的网站
- SGI STL网站,http://www.sgi.com/tech/stl/
- STLport网站,http://www.stlport.org/
- Boost网站,http://www.boost.org/
(译注:如果访问不了,可以试试http://boost.sourceforge.net/)
