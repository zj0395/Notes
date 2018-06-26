# C++ Prime Plus总结
1. 如果一条语句可以解释为函数声明，那它就是函数声明
2. `dec`（十进制）、`hex`（十六进制）、`oct`（八进制）

		cout<<dec<<hexoct;//进制使用方法

3. 特殊函数

	    strcpy( dest , source ); //copy source to dest
		strchr( "wqst", (char)a); //获取a在字符串中的位置

4. 公用体 ：`union`
5. 检验文件打开与否

		ifstream fin( "test.txt" );
		if( ！ fin.is_open() )
		exit(EXIT_FAILURE);//#头文件stdlib

6. `(10*9)/(2*1)`中间值最大90； `(10/2))*(9/1)` 中间值最大10
7. 回调函数：用于与c代码打交道。c++中应用function，兼容更多函数类型
8. auto（c++11）只能用于单值初始化，不能用于列表初始化
9. 格式化控制

		ios_base::fmtflags initial;
        initial = cout.setf(ios_base::fixed);
        ......//输出到流
        cout.setf(initial);//恢复原状态

10. 函数重载的匹配顺序：

	1. 完全匹配，但常规函数优先于模板。  
	2. 提升转换（如char→int, short→int, float→double）  
	3. 标准转换（如int→float, long→double）
	4. 用户定义的转换，如类声明中定义的转换（如char\*→string）  

11. 关键字decltype

		const double* x;
        decltype (x) y;//const double\* y
        const double f(int a);
        decltype ( f(1) ) y;//const double y
        decltype ( (  f(1)  ) ) y;//const double & y
        
12. 关键字volatile：用于可能在外部改变的值，让编译器不对该值进行某种优化
13. new失败时抛出`std::bad_alloc`异常
14. 运算符重载
>成员函数：=、[ ]、( )、->、+=、-=...
>非成员函数：两边参数可互换，可隐式转换。此外应尽量声明为非成员。

15. 显式调用析构函数：在使用定位new时,	应以与创建相反的顺序析构定位new获得的对象。
16. 基类析构函数：公有虚函数或保护非虚函数。
17. valarray：面向数值计算的数组
>兼容STL的方法：`begin(valarray)`, `end(valarray)`   
>`valarray[slice(1,4,1)]=1;`相当于`valarray[1]=valarray[2]=valarray[3]=valarray[4]=1;`
18. 格式化控制

		cout.width(12);//只影响下一个
        cout.fill('-');//设置填充字符
        cout.setf(ios_base::showpoint);
        cout.precision(8);
        
        cout << setw(10);//头文件iomainip中，同样只影响下一个
        
19. iofstream

		ios_base::in;//打开，读取
        ios_base::out;//打开，写入
        ios_base::ate;//打开，指针移至文件尾
        ios_base::app;//只将数据追加到文件尾
        ios_base::trunc;//若已有文件则截短
        ios_base::binary;//二进制文件
        
        ofstream fout("t.txt", ios_base::out | ios_base::app);
        
20. 随机临时文件

		char* tmpnam(char* pszName);//头文件cstdio中
        
21. std::function
>函数名
>函数指针
>函数对象
>lambda表达式
