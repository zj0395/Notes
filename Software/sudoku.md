## 数独的算法
#### 数独规则介绍
规则很简单，就是一个9×9的矩阵。根据现有数字，推算出剩余的所有数字，使每一行、每一列、以及每一个3×3矩阵上都必须有1-9之间的每一个数字。9×9矩阵刚好为9行、9列、9个互不交叉的3×3矩阵。
#### 0. 准备工作
数独是一个矩阵，为了让这个矩阵使用起来简单方便，我们声明一个类，来进行封装  

		typedef std::vector<int>  vint;//头文件vector
		typedef std::vector<vint>  vvint;//二维数组
		class Numbertable
    	{
        public:
            explicit Numbertable();
    	private:
        	vvint vvnum;
        }
#### 1.生成
关于数独的生成，有一个简单一点的算法，就是对一个固定的9\*9数组进行“玩魔方”。  
我们先手动写一个9\*9矩阵  
1 , 2 , 3 , 4 , 5 , 6 , 7 , 8 , 9  
4 , 5 , 6 , 7 , 8 , 9 , 1 , 2 , 3  
7 , 8 , 9 , 1 , 2 , 3 , 4 , 5 , 6  
2 , 3 , 4 , 5 , 6 , 7 , 8 , 9 , 1  
5 , 6 , 7 , 8 , 9 , 1 , 2 , 3 , 4  
8 , 9 , 1 , 2 , 3 , 4 , 5 , 6 , 7  
3 , 4 , 5 , 6 , 7 , 8 , 9 , 1 , 2  
6 , 7 , 8 , 9 , 1 , 2 , 3 , 4 , 5  
9 , 1 , 2 , 3 , 4 , 5 , 6 , 7 , 8  
可以看到我们有了一个很有规律的矩阵，我们现在开始玩魔方，把规律彻底打乱  

* 魔方第一步：数字交换  
数独的难点就在于把1个数字变了之后，相应的会有一连串的数字需要改变。对于我们刚生成的矩阵，假设把第一行第一列的数字由1变成2，相应的这一行和这一列的2需要变成1，这个2相应的行和列上的1需要变成2，这个1相应的行和列上的2需要变成1.....一系列变换之后只是相当于把矩阵上所有的2变成了1，所有的1变成了2，得到的仍是一个合法的数独矩阵。如果我们先把1变成2，再把相应的2变成其它数字的话，3个或更多数字之间混着交换得到的会是一个非法的数独矩阵。  
为了矩阵合法，我们只对两个数字进行交换，把所有的x变成y，所有y变成x。x和y可以是1-9之间的任意数字。  
此时为类增加一个成员函数，用来交换两个值，代码如下：	

		void Numbertable::change_value(int lhs,int rhs)
		{
    		for(vint  & vec : vvnum)//c++11，基于范围的for循环
    	    	for(int &a : vec)
                {
                	a =  a==lhs ? rhs : ( a==rhs? lhs : a );
		        }
		}
可以在初始化矩阵时在一个循环中调用该函数

		std::default_random_engine e(time(0));//c++11，头文件random
		std::uniform_int_distribution<int> u(1,9);//c++11，随机数生成
        for(int a=0;a<15;++a)//15可以该为其它数字
    	{
        	int i=u(e),j=u(e);//两个1-9之间的随机数
            if(i!=j)
        	    change_value(u(e),u(e));
	        else --a;
    	}
        
* 魔方第二步：行、列交换  
第一步做完之后，看上去已经像是一个随机的矩阵了，但有一个很严重的问题。看初始的矩阵，可以看出来，九个1分布在九个位置，第一步不管怎么交换数值，交换前都是1，交换后可能都是6，也可能都是7，反正必定是相同的！所以需要再对列和行进行交换，打破这个规律。  
在交换行和列时需注意，交换不能打破矩阵的合法性。交换行不会打破列的合法行，但可能会打破3×3矩阵的合法性。交换列不会打破行的合法性，但可能打破3×3矩阵的合法性。为了避免这个问题，我们交换行，只在3×3矩阵内部交换。即第一行只能与第二、三行交换，第六列只能与第四、五列交换。其它行、列也一样。  
此时可定义两个函数，交换行和交换列

		void Numbertable::swap_row(int lhs,int rhs)
		{
            using std::swap;
            swap(vvnum[lhs],vvnum[rhs]);
        }
        void Numbertable::swap_col(int lhs,int rhs)
        {
            for(int i=0;i<9;++i)
            {
                using std::swap;
                swap(vvnum[i][lhs],vvnum[i][rhs]);
            }
        }
可以在初始化矩阵时在一个循环中调用该函数

		std::default_random_engine e(time(0));//c++11，头文件random
		std::uniform_int_distribution<int>ul(0,2); 
        for(int a=0；a<10;++a )//10可以该为其它数字
        {
            int bases=3*ul(e),i=bases+ul(e),j=bases+ul(e);
            if(i!=j)
                swap_row(i,j);//i与j均在 0-2 3-5 6-8 三个范围中的一个里
            bases=3*ul(e),i=bases+ul(e),j=bases+ul(e);
            if(i!=j)
                swap_col(i,j);//i与j均在 0-2 3-5 6-8 三个范围中的一个里
        }
       
* 魔方第三步：三行、三列交换  
之前的列和行交换都是局部范围里的交换，为了让更随机，再增加一个交换  
两个函数

        void Numbertable::swap_threecol(int lhs,int rhs)
        {
            for(int i=0;i<3;++i)
                swap_col(lhs+i,rhs+i);
        }
        void Numbertable::swap_col(int lhs,int rhs)
        {
            for(int i=0;i<9;++i)
            {
                using std::swap;
                swap(vvnum[i][lhs],vvnum[i][rhs]);
            }
        }
同样在初始化的时候调用

		std::default_random_engine e(time(0));//c++11，头文件random
		std::uniform_int_distribution<int>ul(0,2); 
        for(int a=0;a<10;++a)//10可以该为其它数字
        {
            i=ul(e),j=ul(e);
            if(i!=j)
                swap_threerow(3*i,3*j);//0 3 6
            i=ul(e),j=ul(e);
            if(i!=j)
                swap_threecol(3*i,3*j);// 0 3 6
        }
        
* 生成矩阵代码汇总  
上面6个函数是分开的，现在将初始化工作合到下面一个函数里面

        void Numbertable::change_Number()
        {
			std::default_random_engine e(time(0));//c++11，头文件random
            std::uniform_int_distribution<int> u(1,9);
            //swap value
            for(int a=0;a<15;++a)//15可以该为其它数字
            {
                int i=u(e),j=u(e);
                if(i!=j)
                    change_value(u(e),u(e));
                else --a;
            }
            std::uniform_int_distribution<int>ul(0,2);
            //swap row and column 
            for(int a=0;a<10;++a)//10可以该为其它数字
            {
                //开始玩魔方，玩魔方的行列顺序可以随意更改
                int bases=3*ul(e),i=bases+ul(e),j=bases+ul(e);
                if(i!=j)
                    swap_row(i,j);// 0-2 3-5 6-8
                bases=3*ul(e),i=bases+ul(e),j=bases+ul(e);
                if(i!=j)
                    swap_col(i,j);//0-2 3-5 6-8
                i=ul(e),j=ul(e);
                if(i!=j)
                    swap_threerow(3*i,3*j);//0 3 6
                i=ul(e),j=ul(e);
                if(i!=j)
                    swap_threecol(3*i,3*j);// 0 3 6
        }
至此矩阵生成完成，只需在`构造函数`中调用函数`change_Number()`即可，构造函数需先讲9×9矩阵设置为初始的矩阵  
构造函数代码如下

		Numbertable::Numbertable():vvnum({
                            {1,2,3,4,5,6,7,8,9},
                            {4,5,6,7,8,9,1,2,3},
                            {7,8,9,1,2,3,4,5,6},
                            {2,3,4,5,6,7,8,9,1},
                            {5,6,7,8,9,1,2,3,4},
                            {8,9,1,2,3,4,5,6,7},
                            {3,4,5,6,7,8,9,1,2},
                            {6,7,8,9,1,2,3,4,5},
                            {9,1,2,3,4,5,6,7,8}})
                            {		change_Number();		}
                            
#### 2.矩阵的显示和难度
数独矩阵生成后，需要默认显示一部分，其它的部分需要玩家来自己推算。可以用一个`bool`表示矩阵中某个数字是显示还是不显示，此时需要一个9×9的`bool`矩阵，也需要一个函数，来生成出这个9×9的`bool`矩阵。  
为了代码的清晰和可扩展，我们再声明出一个类，专门用来设置显示方式，显示方式必须也是随机的。我们可以提供多个函数来设置不同的显示方式，不同函数的设置方式会设置出不同难度的数独游戏（如简单难度会显示出40个数字，困难难度只显示出20个数字；当然难度也有其它依据，可以继续扩展）。  
新类如下：

		typedef vector<bool> vbool;//不能把vector<bool>中的一个bool的地址赋给一个bool指针，详见书籍<<Effective STL>>
        typedef vector<vbool> vvbool;
        class GameLevel
        {
        public:
            explicit GameLevel(int hardlevel);
        private:
        	//私有函数
    		void setlevel();//根据难度调用下4个函数，下4个函数均是设置成员变量ways
            void sayEasyWays();//如果不需要难度可删除这4个函数
            void sayMediumWays();
            void sayHardWays();
            void sayHellWays();
    		void generate_map();//根据成员变量ways设置成员变量defaultshow
            
            //成员变量
            const int level;//难度
            vint ways;//一维数组，数字为每一行中默认显示出几个数字，根据这个设置成员变量defaultshow
            vvbool defaultshow;//9×9的显示方式
        }
* 构造函数

        GameLevel::GameLevel(int a):level(a),
                defaultshow(9, vbool(9,0)),
                ways(9,0)
                {
                	setlevel();
                    generate_map();
                }
其中`defaultshow(9, vbool(9,0))`为把`defaultshow`初始化为9个`vbool(9,0)`，`vbool(9,0)`是9个bool，每个都为0  
* 难度函数，如果不需要可以只留一个。不同函数生成的难度不一样，简单和中等只是个数不一样，困难和地狱则在`generate_map()`中有了对称，难度大增

		static std::default_random_engine e(time(0));//声明为static是因为多个函数都用到了
        void GameLevel::setlevel()
        {
            if(1==level)
                sayMediumWays();
            else if(2==level)
                sayHardWays();
            else if(3==level)
                sayHellWays();
            else
            	sayEasyWays();
        }
        void GameLevel::sayEasyWays()
        {
            //36-39个
            std::uniform_int_distribution<int> us(4,6);
            ways[0]=ways[3]=ways[6]=us(e);
            std::uniform_int_distribution<int> um(3,4);
            ways[1]=ways[7]=um(e);
            ways[2]=ways[5]=ways[8]=int((33-(3*ways[0]+2*ways[1]))/3);
            std::uniform_int_distribution<int> un(4,6);
            ways[4]=un(e);
        }
		void GameLevel::sayMediumWays()
        {
            //30-36
            std::uniform_int_distribution<int> us(3,6);
            ways[0]=ways[3]=ways[6]=us(e);
            std::uniform_int_distribution<int> um(2,3);
            ways[1]=ways[7]=um(e);
            ways[2]=ways[5]=ways[8]=int((28-(3*ways[0]+2*ways[1]))/3);
            std::uniform_int_distribution<int> un(3,5);
            ways[4]=un(e);
        }
        void GameLevel::sayHardWays()
        {
            //29-31
            std::uniform_int_distribution<int> us(3,5);
            ways[0]=ways[8]=us(e);
            ways[1]=ways[7]=7-ways[0];
            std::uniform_int_distribution<int> ud(2,3);
            ways[2]=ways[6]=ud(e);
            ways[3]=ways[5]=7-ways[2];
            std::uniform_int_distribution<int> ul(1,3);
            ways[4]=ul(e);
        }
        void GameLevel::sayHellWays()
        {
            //27-29
            std::uniform_int_distribution<int> us(3,5);
            ways[0]=ways[8]=us(e);
            ways[1]=ways[7]=6-ways[0];
            std::uniform_int_distribution<int> ud(2,3);
            ways[2]=ways[6]=ud(e);
            ways[3]=ways[5]=7-ways[2];
            std::uniform_int_distribution<int> ul(1,3);
            ways[4]=ul(e);
        }
* 根据`ways`生成`defaultshow`的函数如下

            void GameLevel::generate_map()
            {
                if(level<2)
                    for(int a=0;a<9;++a)
                    {
                    	changeRow(defaultshow[a],ways[a]);
                    }
                else
                {
                    for(int a=0;a<5;++a)
                    {
                    	changeRow(defaultshow[a],ways[a]);
                    }
                    for(int a=5;a<9;++a)
                    {
                        defaultshow[a]=defaultshow[8-a];//困难的地狱的对称
                    }
                }
            }
其需要的`changeRow`函数如下，该函数没有用到任何成员变量和成员函数，可放到类外

        void GameLevel::changeRow(vbool &a,int num)
        {
            int s=0;//设置为1的数字的个数
            std::uniform_int_distribution<int> un(1,9-s);//范围
            int n=un(e);//e仍然是难度函数的前面声明为static的引擎，在上面的范围里产生一个随机数，第n个数字设置为1，即显示
            int temp=0;
            vbool::iterator i=a.begin();
            do{
                if(0==*i)
                	temp++;
                if(temp==n)
                {
                    *i=1;
                    if(++s==num)break;
                    i=a.begin();//继续从头开始循环
                    std::uniform_int_distribution<int>ud(1,9-s);
                    n=ud(e);temp=0;
                }
                else ++i;
            }while(i!=a.end());
        }
* 使用难度，在`Numbertable`类的构造函数中增加以下语句

		int hardclass;
        hardclass=1;//此处让玩家设置
        GameLevel lev(hardclass);
        for(int a=0;a<9;++a)
        {
            for(int b=0;b<9;++b)
            {
                if(!lev[a][b])//如果不为1
                    vvnum[a][b]=0;//就把值设为0，即不显示
            }
        }
由于用到了`GameLevel`的操作符重载，需要在`GameLevel`中添加操作符重载函数，当然也可以其它方法
		
		const vbool & operator[](int a)const
        {
        	return defaultshow[a];
        }
#### 3. 其它功能
此坑待填...功能都写好了，但注释比较少
##### 1. 错误提示
##### 2. 帮助
##### 3. 存/读档