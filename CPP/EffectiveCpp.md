# Effective C++
### 1\. 视 C++ 为一个语言联邦  
C , *pass-by-value*  
Object-Oriented C++ , *pass-by-reference*  
Template C++  
STL，对于迭代器和函数对象，*pass-by-value*  
C++ 高效编程守则视状况而变化，取决你使用 C++ 的哪一部分。

### 8\. 析构函数吐出异常就是危险  

### 9\. 绝不在构造函数和析构函数过程中调用`virtual`函数  

### 10\. 令赋值运算符返回一个 `reference to *this`  

### 11\. copy and swap  

### 12\. 不要尝试以`copying构造函数`实现另外一个`copying assignment运算符`，或是反过来。应将共同机能放进第三个函数中  

### 13\. 以对象管理资源。
>动态分配而得的array身上使用智能指针是个馊主意。  

因为智能指针的析构函数内做的是delete，而不是delete \[]。

### 14\. 资源管理类中小心`copying`行为
>禁止复制  
>引用计数  
>复制底部资源  
>转移所有权

`shared_ptr<Typename>`允许指定删除器

		CloseFile(FILE* fp)
        {
        	if( fp )
            	fclose(fp);
        }
		shared_ptr<FILE> fp( fopen("file"), CloseFile() );

### 15\. 资源管理类并不是为了封装而存在，而是为了确保资源会被释放

### 16\. 不要对数组形式做`typedef`动作。不然delete的时候会让自己混乱

### 17\. 以独立语句将newed对象直入智能指针  
即吧创建智能指针对象的语句与其它语句分离，以避免可能的内存泄露。  
对于这样一条语句

		processWidget( std:shared_ptr<Widget>(new Widget), priority() );

共做了三件事：调用`priority`，执行`new Widget`，调用`shared——ptr`构造函数。编译器以何种次序完成这三件事是不确定的。`priority`的调用可能夹在后两个操作之间，这样如果调用`priority`时有异常抛出，`new Widget`的内存就泄露了。

### 19\. 设计class犹如设计type

### 22\. 所有反对public成员变量的论点同样适用于protected成员变量。所以封装形式只有两种，private与非private

### 23\. namespace可以跨越多个源码文件

### 24\. 若所有参数皆需类型转换，请为此采用non-member函数  
因为成员函数必须需要一个对象去调用，相当于已经绑定了第一个参数  
>无论何时如果你可以避免friend函数就该避免，因为就像真实世界一样，朋友带来的麻烦往往多过其价值。当然有时候friend有其正当性，但这个事实仍然存在：不能够只因函数不该成为member，就自动让它成为friend。

### 25\. c++允许对 class template 偏特化；不允许对 function template 偏特化，应给它添加一个重载版本。  
全特化就是针对模板类型中特定的一种类型，只包括一种
偏特化就是针对模板类型中特定的一类类型，这一类中可能有好多种

### 27\. 尽量少做转型动作  
const\_cast 通常移除常量性，唯一有此能力的c++转型操作符  
dynamic\_cast 安全向下转移，唯一可能耗费重大运行成本的转型动作，要对此保持机敏与猜疑  
reinterpret\_cast 不可移植
static\_cast 强迫隐式转换

### 28\. 返回一个 handle 代表对象内部成分总是危险的。  
因为这个 handle 可能比其所指的东西更加长寿。

### 31\. 将文件间的编译依存关系降至最低  
如果能够，尽量以 class 声明式替换 class 定义式。即使是 by value 传参，也不需要该类的定义  
标准头文件不太可能成为编译瓶颈，而且本来就不该尝试手工声明一部分标准库。直接 #include 。

### 32\. 确定你的 public 继承塑模出 is-a 关系  
>至于 protected 继承，那是一种其意义至今仍然困惑我的东西。

### 34\. 区分接口继承和实现继承  
>公有继承：  
>纯虚函数 ： 只继承接口  
>非纯虚函数：继承接口和默认实现  
>非虚函数：继承接口和一份强制实现

### 35\. 考虑 virtual 函数以外的其它选择  
籍由 NVI: Non-virtual Interface 完成 Templates Method 模式  
籍由 Function Pointers 实现 Strategy 模式  
籍由 std::function 完成 Strategy 模式： std::fucntion 可封装函数指针、函数对象、成员函数指针（借助bind）、lambda

### 37\. 绝不重新定义继承而来的缺省参数值  
>因为缺省参数值都是静态绑定，而 virtual 函数——你唯一应该覆写的东西——却是静态绑定的。

### 39\. 私有继承只继承实现

### 40\. 明智而审慎地使用多重继承  
virtual 继承是有代价的，时间慢，空间大  
如果必须使用 virtual base classes，尽可能避免在其中放置数据

### 41\. 对classes而言接口是显示的，以函数签名（参数类型和个数）为中心。多态则是通过virtual函数发生与运行期。  
对template参数而言，接口是隐式的，取决于你在模板函数或类中使用的语句。多态则是通过template具现话和函数重载解析发生与编译期。  

### 42\. 嵌套从属类型名称：依赖与某个模板参数  

		temlpate<typename T>
        void printf1st( const T& container)
        {
        	T::const_iterator iter( container.begin() );//编译器并不会认为
            ...										//T::const_iterator是个类型 
            typename T::const_iterator iter2( container.begin() );//this is ok
        }

编译器并不会把上面的`T::const_iterator`视为类型，除非你告诉它是。上面语句倒数第二行就是告诉编译器这是一个类型。  
typename只应用于嵌套与模板类型的类型，其它名称中不应该用typename  

		//前一个参数不能加typename，后一个参数必须加  
		template<typename T>
        void f( const T& container, typename T::iterator iter);
        
此外在类的构造函数的member initialization list（成员初始化列表）中不能也使用typename。
>typename和“嵌套从属类型名称”之间的互动，也许会在移植性方面带给你某种温和的头疼  

### 43\. “当base classes从template中被具现化时”，它假设它对那些base classes的内容毫无所悉。

		template<typename Company>
        class MsgSender
        {
        	void sendClear(const MsgInfo& info);
            void sendSecert(const MsgInfo& info);
        };
        
		template<typename Company>
        class LoggingMsgSender : public MsgSender<Company>
        {
        	void sendClearMsg( const MsgInfo& info)
            {
            	sendClear(info);//无法通过编译
            }
        };
        
编译器并不认为基类即上面代码中的`MsgSender`类中存在`sendClear`函数。因为基类是一个模板类，编译器清楚的知道基类可以被特化，而那个特化版本可能并不提供和template相同的接口。  

		class CompanyZ
        {
        	void sendEncrypted(const std::string& msg);
        };
		template<>
        class MsgSender<CompanyZ>
        {
        	void sendSecert(const MsgInfo& info);
        };

如上代码所示，特化了`MsgSender<CompanyZ>`，对于特化的该类而言并不存在`sendClear`函数。所以对于派生类`LoggingMsgSender`而言编译无法通过是正确的选择。  
有三种方法可以让这个通过编译  
方法1：
              
        template<typaname Conmany>
        class LoggingMsgSender: public MsgSender<Company>
        {
        	void sendClearMsg( const MsgInfo &info)
            {
            	this->sendClear(info);//添加了this->，假设sendClear会被继承
            }
        };
        
方法2：

		template<typaname Conmany>
        class LoggingMsgSender: public MsgSender<Company>
        {
        	using MsgSender<Company>::sendClear;//告诉编译器，请它假设sendClear位于基类内
            
        	void sendClearMsg( const MsgInfo &info)
            {
            	sendClear(info);
            }
        };

方法3：

		template<typaname Conmany>
        class LoggingMsgSender: public MsgSender<Company>
        {
        	void sendClearMsg( const MsgInfo &info)
            {
            	MsgSender<Company>::sendClear(info);//假设sendClear会被继承
            }
        };
        
方法3最不令人满意，因为明确的资格修饰会关闭“virtual绑定行为”。  
>从名称可视点（visibility point）的角度出发，上诉每一个解法做的事情都相同；对编译器承诺“base class template的任何特化版本都将支持其一般（泛化）版本所提供的接口”。这样一个承诺是编译器在解析（parse）像LoggingMsgSender这样的derived class template时需要的。但如果这个承诺最终未被实践出来，往后的编译器最终还是会还给事实一个公道。

		LoggingMsgSender<ComPanyZ> zMsgSender;
        MsgInfo msgData;
        ...
        zMsgSender.sendClearMsg( msgData);//无法通过编译，ComPanyZ没有sendClear函数

>C++的政策是宁愿较早诊断，这就是为什么“当base classes从template中被具现化时”，它假设它对那些base classes的内容毫无所悉的缘故。

### 44\. 将与参数无关的代码抽离templates  
防止代码膨胀。  
也把所有指针的templates共用一份底层实现。

### 46\. 需要类型转换时请为模板定义非成员函数  
template实参推导过程中，从不将隐式类型转换考虑在内。  
class templates并不依赖template实参推导。  
>这项技术的一个趣味点是，我们虽然使用friend，却与friend的传统用途“访问class的non-public成分”毫不相干。为了让类型转换可能发生于所有实参身上，我们需要一个non-member函数（条款24）；为了令这个被自动具现化，我们需要将它声明在class内部；而在class内部声明non-member函数的唯一方法就是：令它成为一个friend。因此我们就这样做了。不习惯？是的。有效吗？不必怀疑。  
>“令friend函数调用辅助函数”的做法的确值得研究一番

### 47\. 请使用 traits classes 表现类型信息  
traits classes 使得“类型相关信息”在编译期可用。  
整合重载技术后，traits classes 有可能在编译期对类型执行if...else测试。

### 48\. 认识 template 元编程  
编译器必须确保所有的源码都有效，纵使是不会执行起来的代码。

### 51\. 编写 new 和 delete 时需固守成规  
c++裁定所有非附属（独立式）对象必须有非零大小。  
operator new 会被派生类继承  

# Effective STL
### 18\. 避免使用`vector<bool>`
>vector< bool>是一个伪容器，并不保存真正的bool，而是打包bool以节省空间。

### 19\. 了解相等和等价的差别
>a==b  和  !(a < b)&&!(b < a)

### 20\. 
