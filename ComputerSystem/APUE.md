# Adavance Programming in the UNIX Environment
[书中代码](https://github.com/zj0395/myCode/tree/master/APUE)
## 一、UNIX 基础知识
#### UNIX 体系结构
- 严格来讲，操作系统可定义为一种软件，控制计算机硬件资源，提供程序运行环境。通常将这种软件称为内核（kernel）
- 内核接口被称为系统调用（system call）
#### 登录
- /ect/password 中查看登录项。
    zj:x:1000:1000:zj,,,:/home/zj:/bin/bash
    依次是登录名:加密口令:数字用户ID:数字组ID:注释字段:起始目录:shell程序
#### 文件和目录
- ['ls'命令的简单实现](https://github.com/zj0395/myCode/blob/master/APUE/1-3_ls.c)
- 文件描述符（file descriptor）通常是一个小的非负整数，内核用以标识一个特定进程正在访问的文件
- 每一个新程序运行时，shell都会为其打开 standard input, standard output, standard error
- 函数 open、read、write、lseek 以及 close 提供了不带缓冲的I/O
- 标准 I/O 函数为不带缓冲的 I/O 函数提供了一个带缓冲的接口
#### 程序和进程
- 程序是存储在磁盘上某目录下的可执行文件
- 程序的执行实例被成为进程（process）
- UNIX 系统保证每个系统都有一个唯一的进程ID。总是一个非负整数
- 有三个进程控制函数: fork、exec 和 waitpid
- 一个进程内的所有线程共享同一地址空间、文件描述符、栈以及与进程相关的属性
- **每个线程也拥有各自的栈**
#### 出错处理
- Linux 中，出错常量在 errno(3) 手册页中列出
- errno 需注意，仅当函数返回值指明出错时，才去校验 errno；任何函数都不会将 errno 值设为0
- `char* strerror(int errnum)` 和 `void perror(const char* msg)` 用于输出出错消息
- 对于非致命性错误，可使用指数补偿法，延长一段时间
#### 用户标识
- 用户ID为0的是root或superuser
- 组文件将用户映射到不同的组ID。组文件通常是 /etc/group
#### 信号
- 通常有三种处理方式
    1. 忽略信号
    2. 按系统默认方式处理。对于除数0，默认方式是终止进程
    3. 提供一个函数，信号发生时调用，这被称为捕捉该信号
#### 时间值
- 类型 time_t 用于保存日历时间，从1970.1.1.0:0:0开始至今的秒数
- 类型 clock_t 保存进程时间，度量 CPU 资源
- 时钟时间：进程运行的时间总量
- 用户 CPU 时间：执行用户指令所用的时间量
- 系统 CPU 时间：执行内核程序所经历的时间
#### 系统调用和库函数
- 各种版本的UNIX系统都提供良好定义、数量有限、直接进入内核的入口点，这些入口点称为系统调用
- 从应用角度，可将系统调用视为C函数
- UNIX 系统调用中处理空间分配的是`sbrk`。它按指定字节数增加或减少进程地址空间。如何管理该地址空间取决于进程。
- 有很多软件包h使用`sbrk`系统调用来实现自己的存储空间分配算法
- 系统调用通常提供最小接口，而库函数通常提供比较复杂的功能
## 二、UNIX 标准及实现
## 十一、线程
- 每个线程都包含有表示执行环境所必需的信息，其中包括进程中标识线程的线程ID、一组寄存器值、栈、调度优先级和策略、信号屏蔽字、errno变量以及线程私有数据
- POSIX 线程的功能测试宏是`_POSIX_THREADS`，位于`<unistd.h>`里
- 线程 ID 只有在它所属的进程上下文中才有意义
#### 线程创建
- 新创建的线程可以访问进程的地址空间，并且继承调用线程的浮点环境和信号屏蔽量，但是该线程的挂起信号集会被清除
- `pthread_t pthread_self()`获得本线程的id
#### 线程终止
- 三种返回方式
    1. 用`return`返回
    2. 被同进程中的其它线程`pthread_cancel`。返回值的指针只想的内存单元被设置为`PTHREAD_CANCELED`
    3. 线程调用`pthread_exit`
- `pthread_cleanup_push` 和 `pthread_cleanup_pop` 用于清理线程，用的栈
    1. 调用 `pthread_exit` 时
    2. 响应取消请求时
    3. 用非零参数调用 `pthread_cleanup_pop` 时
- `pthread_detahc` 分离线程，分离后将无法使用`pthread_join`
#### 线程同步
- 增量操作通常分解为以下3步
    1. 从内存单元读入寄存器
    2. 在寄存器中对变量做增量操作
    3. 新的值写回内存单元
##### 互斥量
- `pthread_mutex_lock`、`pthread_mutex_trylock`、`pthread_mutex_unlock`
##### 避免死锁
- 仔细控制互斥量加锁的顺序
- 可以先释放占有的锁，过一段时间再试，使用`pthread_mutex_trylock`
