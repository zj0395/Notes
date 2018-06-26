# 数独—暴力求解算法
## 1.基本工作
数独是9×9的矩阵，为了代码清晰、使用方便，声明一个类进行封装

		typedef std::vector<int> vint;//头文件vector
        typedef std::vector<vint> vvint;//二位数组
        class SudokuCrack
        {
        public:
            SudokuCrack(vvint &);
            int beginCrack();//开始求解
        private:
            vvint vvnum;//0表示没有数字，1-9为正常数字
            static vvint result;//用于保存结果，静态成员变量，需要在cpp文件中初始化
        };
现在这个类简单的一个成员变量，一个构造函数还有一个接口函数。构造函数直接接受一个二维数组。
## 2.求解函数
我们的基本思路是按照人做数独题的方式去求解。  
* 选择最优点  
我们在做数独题的时候会大概看一遍整个9×9矩阵，选择一个周围数字比较多的点来，周围的数字越多，这个点上可排除的数字就越多，可能的情况是最少的。  
我们就按照人的思路来，先遍历9×9矩阵，选择一个缺少的数字的数量最少的点，当做最优点，“猜”这个点上的数字。  
定义一个选点函数：  

		static std::default_random_engine e(time(0));//c++11，需要头文件random，windows可能还需要头文件ctime
		static std::uniform_int_distribution<int> u(0,1);//随机数，0和1，用于当最优点有好几个时进行随机选择
        //该函数返回值为int，不是bool，这样我们可以发生错误时返回一个错误代码
        int SudokuCrack::selectBetter()
        {
            vint min_locate ( 2,0 );//保存选择的点的坐标
            int min_lack=10;//可能的最大值是9，这个这是初始值，用于被代替
            int lack_num = 10;
            for( int i=0 ; i<9 ;++i )
            {
                for( int j=0; j<9; ++j )
                {
                	//此处先进行判断，如果这个点上的有数字，那这个点可能的数字个数就是10
                    //如果这个点上没有数字，那就计算这个点上可能的数字的个数
                    lack_num =  vvnum[i][j] ? 10 :  countLackNum(i,j);
                    if( lack_num < min_lack  )//该点是目前的最优点
                    {
                        min_lack = lack_num;
                        min_locate[0]=i;//保存坐标
                        min_locate[1]=j;
                    }
                    else if(lack_num == min_lack)//缺少的数字个数一样，都是最优点
                    {
                        if( u(e)  )//就随机进行选择
                        {
                            min_locate[0]=i;
                            min_locate[1]=j;
                        }
                    }
                }
            }
            if( min_lack == 10 )//最小的点上缺少的个数为10个，就表明所有点上都有数字了，没有下一步可选了，只能判断是否胜利
            {
                if( checkwin() )//如果胜利了
                {
                    result=vvnum;
                    return 1;//返回1求值成功，不再继续计算
                }    
                else return 0;//不然就失败
            }
            return attemptLocate( min_locate[0]  ,min_locate[1] );//对最优点一个一个的“猜”，猜的结果返回
        }
这个选择最优的点的函数用到了另外三个函数`countLackNum(i,j)`、`checkwin()`和`attemptLocate(i,j)`。我们先继续按照思路走，这些函数先放着最后再写

* “猜”最优点上缺少的数字  
首先需要确定这个这个点上缺少哪些数字，然后对这个点上可能的数字进行一个一个的试。我们首先想到的用一个固定的1-9数组，每当存在一个数字我们就从这个数组中删去这个数字，这个数组上需要的操作只有删除。如果删除数字时可以不用检查数组中是否包含这个数字，操作会方便很多。最后决定选择set作为容器，可以简单且快速的的进行删除  
两个函数：
		
        typedef std::set<int> sint;//需要头文件set
        int SudokuCrack::attemptLocate( int i, int j )//“猜”数字的函数
        {
            sint d = getLackedNumSet( i , j );//保存有该点可能的所有数字
            sint::iterator it=d.begin();//迭代器，用于遍地所有数字，一个一个试
            for( ; it!=d.end(); ++it )
            {
                vvint vvnum_new=vvnum;//新的9×9矩阵
                vvnum_new[i][j]=*it;
                SudokuCrack sucr_t(vvnum_new);
                if( sucr_t.selectBetter() )//对新矩阵进行求解
                    return 1;
            }
            return 0;
        }
        sint& SudokuCrack::getLackedNumSet(int i,int j )
        {
            static sint d;//静态成员
            sint s_temp={1,2,3,4,5,6,7,8,9};//一个1-9的数组
            using std::swap;
            swap(d , s_temp);//使用swap进行交换，避免复制
            for(int m=0; m<9; ++m)
            {
                d.erase(vvnum[i][m]);//删除行上的数字，数字是0-9之间的任意值，不用进行检查
                d.erase(vvnum[m][j]);//删除列上的数字
            }
            int m =i/3*3, n=j/3*3;
            for( int q=0; q<3; ++q )
            {
                for( int p=0 ;p<3; ++p )
                {
                    d.erase( vvnum[m+q][n+p] );//删除3×3方格上的数字
                }
            }
            return d;
        }
        
* 缺少的函数  
至此主要矛盾已解决，上述代码将递归求解数独矩阵。  
现在把上面缺少的函数`countLackNum(i,j)`和`checkwin()`补充进去就可以运行了  

        int SudokuCrack::countLackNum(int i ,int j)
        {
            return getLackedNumSet(i,j).size();//调用已有函数
        }
        //用了set确实很方便，插入的时候也不会重复插入
        bool SudokuCrack::checkwin()
        {
        	//sint见上一节的typedef
            const sint ss={1,2,3,4,5,6,7,8,9};//固定的1-9，用于检查是否和它相等
            for( int i=0 ;i<9; ++i )
            {
                sint a,b;
                for(int j=0; j<9; ++j)
                {
                    a.insert(vvnum[i][j]);//每行的数字集合
                    b.insert(vvnum[j][i]);//每列的数字集合
                }
                if( a!=ss || b!=ss )//任意一个不相等，就没有求解成功
                    return 0;
            }
            for( int m=0 ;m<7; m+=3 )//四重循环，但每一重只有3个
            {
                for( int n=0 ;n<7; n+=3 )//3×3个
                {
                    sint d;
                    for(int i=0 ;i<3; ++i)//3×3宫格
                    {
                        for( int j=0 ;j<3; ++j )
                        {
                            d.insert(vvnum[m+i][n+j]);
                        }
                    }
                    if(d!=ss)
                        return 0;//求解失败
				}
            }
            return 1;//求解成功！
        }
至此数据的求解已经完成，但还有一些地方需要补充
## 3.运行前错误检查
我们不能寄希望于用户提供的数字是完美的，相反，用户提供的数字常常可能伴着错误，比如直接在同一行里输入了两个1。如果带着这些错误运行，程序不卡死也会陷入巨大数量的循环中，几乎会对没有提供的数字集合进行一个遍历。假设用户提供了20个数字，其中有一行有两个1，那么程序会对剩下的71个点，每个点有n种（1< n< 9）可能，程序会进行n^71次计算，，最后返回一个0表示失败。这是一个不可能完成的任务。  
所以我们需要提前检查错误，发现错误直接返回，不进入递归中。  
定义一个函数`isBad()`用于运行前检查，同样选择set，可以快速插入，快速查找  

        int SudokuCrack::isBad()
        {
            for( int i=0 ;i<9; ++i )
            {
                sint a,b;//sint见前面的typedef
                for(int j=0; j<9; ++j)
                {
                    if(vvnum[i][j]!=0)//数值为0就跳过
                    {
                        if( a.find(vvnum[i][j])==a.end() ) //是否找到了
                            a.insert(vvnum[i][j]);//没找到就插入
                        else//找到了相同值就报错
                        {
                            return 1;//错误代码1
                        }
                    }
                    if(vvnum[j][i]!=0)
                    {
                        if( b.find(vvnum[j][i])==b.end() )
                            b.insert(vvnum[j][i]);
                        else//找到了相同值就报错
                            return 2;
                    }
                }
            }
            for( int m=0 ;m<7; m+=3 )//3×3×3×3的四重循环
            {
                for( int n=0 ;n<7; n+=3 )//3×3个
                {
                    sint d;
                    for(int i=0 ;i<3; ++i)//3×3矩阵
                    {
                        for( int j=0 ;j<3; ++j )
                        {
                            if(vvnum[m+i][n+j]==0)//值为0就跳过
                                continue;
                            if( d.find(vvnum[m+i][n+j])==d.end() ) 
                                d.insert(vvnum[m+i][n+j]);
                            else
                            {
                                return 3;//错误代码3
                            }
                        }
                    }
                }
            }
            return 0;//用户的输入正确
        }
添加了错误处理，就要在代码中用到，我们的`beginCrack()`函数只在第一次调用，正是放置运行前错误处理的地方，`beginCrack()`如下

        int SudokuCrack::beginCrack()
        {
            int n=isBad();
            if( n!=0 )
                return n;
            result = vvint(1,vint(1,0));
            selectBetter();
            return 0;
        }
在使用的时候可根据`beginCrack()`的返回值不同，进行不同的错误处理。返回为0则正常运行。可以定义一个静态成员函数用来获取求解的结果  

   	 const vvint& getResult(){	return result	;	}
## 4.代码实例
至此我们有了一个稳健的求解方法，下面是一个实例  

        #include<iostream>
        #include "sudokucrack.h"
        using namespace std;
        int main()
        {
            vvint d{
                {4,0,0,5,2,0,7,0,3},
                {0,0,0,0,0,3,0,0,0},
                {1,0,0,0,0,7,0,0,0},
                {0,1,4,0,0,0,0,0,6},
                {7,0,0,0,5,0,0,0,1},
                {5,0,0,0,0,0,4,2,0},
                {0,0,0,4,0,0,0,0,5},
                {0,0,0,8,0,0,0,0,0},
                {2,0,1,0,7,6,0,0,8}
            };
            SudokuCrack s(d);
            int m=s.beginCrack();
            if(m==0)
           	{
            	const vvint &r=s.getResult();
                for(const vint & a : r)//c++11，基于范围的for循环
                {
                	for( int d : a)
                    	cout<<d<<" ";
                    cout<<"\n";
                }
                cout<<endl;
            }
            else if(m==1)
            	cout<<"数独非法！在某行中有重复数字！"<<endl;
            else if(m==2)
            	cout<<"数独非法！在某列中有重复数字！"<<endl;
            else if(m==3)
            	cout<<"数独非法！在某3×3矩阵中有重复数字！"<<endl;
            return 0;
        }
瞬间完成，输出如下

        4 9 6 5 2 8 7 1 3 
        8 7 5 1 4 3 6 9 2 
        1 3 2 9 6 7 8 5 4 
        3 1 4 2 8 9 5 7 6 
        7 2 9 6 5 4 3 8 1 
        5 6 8 7 3 1 4 2 9 
        6 8 7 4 9 2 1 3 5 
        9 4 3 8 1 5 2 6 7 
        2 5 1 3 7 6 9 4 8 

