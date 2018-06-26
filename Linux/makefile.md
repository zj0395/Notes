# Makefile
### 执行时选项
`-jN` 指定N个进程同时编译，编译东西太多时用以提升速度  
`-k` 发现错误也继续执行  
`-n` 输出将要执行的步骤，但不执行  
`-f filename` 将 filename 文件当做makefile  
如未指定 all，则只创建它找到的第一个目标  
### 变量
`$?` 当前目标所依赖的文件中比当前目标还新的  
`$@` 当前目标的名字  
`$^` 所有当前依赖的名字  
`$<` 为第一个依赖文件  
### 函数
##### wildcard
`SRC = $(wildcard *.c ./foo/*.c)`  
搜索当前目录及 ./foo/ 目录下所有以 .c 结尾的文件，生成一个以空格为间隔的列表，赋值给 SRC，当前目录下的只有文件名，子目录下的还带有路径信息  
##### notdir
`FILENAME = $(notdir $(SRC))`  
去除所有的目录信息  
##### patsubst
`OBJ = $(patsubst %.c %.o $(FLIENAME))`  
在 FILENAME 中找到所有以 .c 结尾的文件，然后把 .c 转换成 .o 赋值给 OBJ  
`OBJ = $(FILENAME:.c=.o)`  
与使用 patsubst 效果相同