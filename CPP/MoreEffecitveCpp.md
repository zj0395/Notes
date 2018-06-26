# More Effective C++
### 3. 绝不要以多态方式处理数组
- 多态和指针算术不能通用。数组对象几乎总是会涉及指针的算术运算，所以数组和多态不要混用。  
- 如果能避免让一个具体类继承自另外一个具体类，你就不太能够犯“以多态方式来处理数组”的错误。

### 4. 非必要不提供 default constructor
- 缺少 default constructor 的类将不兼容于许多（不够严谨的）templates。  
- `std::vector` 不要求类型具有默认构造函数，因为可以显式初始化多个值。

### 5. 对定制的“类型转换函数”保持警觉
- 隐式的类型转换函数，害处多过好处

### 7. 千万不要重载 &&、|| 和， 运算符
- 会破坏掉这些运算符本身的短路求值属性。你无法模仿这些运算符本身短路求值的属性。

### 8. 了解各种不同意义的 new 和 delete
- `string str = new string("Test");`这一条语句调用的是 new operator，分配内存并调用 constructor；只有编译器有权利调用 constructor，程序员没有权利。  
- `void * opeartor new (size_t n);`这一条语句调用 operator new，只分配内存，没有任何 constructor 会被调用。   

>如果你打算在 heap objects 产生时自己决定内存分配方式，请写一个自己的 operator news，并使用 new operator，它将会自动调用你所写的 operator new。如果你打算在已分配（并拥有指针）的内存中构造对象，请使用 placement new。

### 10. 在 constructors 内阻止资源泄露（resource leak）

		class BookEntry
        {
        public:
        	BookEntry(const string& imageName, const string& audioName) : 
            		theImage( new Image(imageName) ), theAudio( new Audio(audioName) )
        private:
        	const shared_ptr<Image> theImage;
            const shared_ptr<Audio> theAudio;
        };
        
使用智能指针免除了“ exceptions 出现时发生资源泄露”的危机。把“构造过程中可能发生的 exceptions”这个棘手的问题变得简单了许多。

### 13. 以 by reference 传递异常
- 没有切割问题，没有指针的谁来释放问题，效率还高

### 16. 谨记 80-20 法则
- 一个程序80%的资源用于20%的代码身上。
- 辨识之道就是借助某个程序分析器（progame profiler），而不是猜

### 17. 考虑使用 lazy evaluation（缓式评估）
- 缓式取出
- 表达式缓评估。如计算矩阵的乘积，实际上只用了乘积的一小部分。

### 18. 分期摊还预期的计算成本
- 多取出一点
- 以空间换时间

### 19. 了解临时对象的来源
- 隐式类型转换会产生临时对象
- 函数返回对象时也可能产生临时对象，不过大部分编译器都可实行 RVO（返回值优化）

### 21. 利用重载技术避免隐式转换
>增加一大堆重载函数并不见得是件好事，除非你有好的理由相信，使用重载函数后，程序的整体效率可获得重大的改善。

### 23. 考虑使用其他程序库
- iostream 具有类型安全（type-safe）特性，并且可扩充。然而在效率方面，iostream 通常表现的比 stdio 差，因为 stdio 的可执行文件通常比 iostream 更小也更快。不过对于一个真正有用的程序而言，两者所造成的可执行文件大小差别应该不大。
- 性能评估软件还是可以再“不同做法之间的性能比较”上助我们一臂之力。完全依靠和完全忽略它都是愚蠢的行为。

### 24. 了解 virtual function、multiple inheritance、virtual base classes、runtime type identification的成本
- 大部分编译器都使用所谓的 virtual tables 和 virtual table pointers。二者简写为 vtbls 和 vptrs。
- 选择了虚函数，便放弃了 inline。

### 25. 将 constructor 和 non-member-functions 虚化
- 二者都无法真正被虚化。然而我们能够以某个函数根据不同的输入而构造不同类型的新对象，也可以让 non-member-functions 的行为视其参数的动态类型而不同。
- NVI，非虚接口。写一个虚函数做实际工作，但提供一个非虚接口。

>有一种特别的 virtual constructor——所谓 virtual copy constructor——也被广泛运用。Virtual copy constructor 会返回一个指针，指向其调用者（某对象）的一个新副本。基于这种行为，通常命名为 copySelf 或 cloneSelf。

### 26. 限制某个 class 所能产生的对象数量
- 构建一个用来计数的基类

### 27. 要求或禁止对象产生于 heap 中
- 要求对象产生与 heap 中
	- 让 destructor 成为private
	- 实际上只是限制了一个语法的使用。与`myClass object;`类似的语句不能再使用，即使是在 heap 内

- 没有可移植的方法判断某个对象是否位于 heap 内
- 禁止对象产生于 heap 中
	- operator new 与 operator delete 私有即可
	- 但会被派生类继承，影响派生类使用

### 28. Smart Pointers
- 一个基类
- 你绝对无法成功设计出一个泛用型 smart pointer，可以完全无间隙地取代 dumb pointer。

### 29. Reference countings
- 结构清晰，继承关系明了

### 30. Proxy classes
- 区分右值引用和左值引用
- 但限制更多

### 31. 让函数根据一个以上的对象类型来决定如何虚化
>C++并不支持 double-dispatching，所以必须自己完成“编译器对虚函数的实现工作”。可能性之一就是舍弃C++，选用另一种程序语言。

- 虚函数 + RTTI。不再封装良好，每个类中虚函数都要知道其兄弟类，且程序难以维护。
- 只使用虚函数。使用大量的重载。致命缺陷：每增加一个类，就要在体系中每一个类的函数里面加一个重载函数。

>简单地说，如果你需要在程序中实现 double-dispatching，最好的方向就是修改设计，消除此项需求。如果不能，那么虚函数法比 RTTI 法安全一些，但是如果你对头文件的权力不够，这种做法会束缚你的系统扩充行=性。至于 RTTI 法，虽不需要重新编译，却往往导致软件难以维护。你总是得付出代价，才能获得机会。

- 自行仿真虚函数表格（Virtual Function Tables）
	- 每个类一个map，其它类的type\_info 均对应1个函数
	- 有多少个其它类就有多少个函数
	- 搜索map，确定使用哪个函数

- 使用“非成员函数”的处理函数
	- 所有类共用一个map，两个类对应1个函数

没有完美的方法实现出 double dispatch，但此法我们可以轻松完成一个以 map 为基础的实现品——如果这种做法最吻合我们需要的话。

- 匿名 namespace 内的每样东西对其所驻的编译单元（文件）而言都是私有的，其效果就好像在文件里头将函数声明为 static 一样。由于 namespace 的出现，“文件生存空间（file scope）内的static”已经不再继续被鼓励使用。

### 33. 将非尾端类（non-leaf classes）设计为抽象类（abstract classes）
>不太可能设计得出一个令人满意的“抽象”封包类，除非你对于多种不同的封包格式造诣深厚，并且知道在不同的环境下如何使用他们。面对你那有限而薄弱的经验，我的忠告是不需要为封包定义一个抽象类。日后当你发现有“从具体封包类继承下来”的需要是，才补上一个抽象类就好。
