# Adavance Programming in the UNIX Environment
[书中代码](https://github.com/zj0395/myCode/tree/master/APUE)
## 一、UNIX 基础知识
### UNIX 体系结构
- 严格来讲，操作系统可定义为一种软件，控制计算机硬件资源，提供程序运行环境。通常将这种软件称为内核（kernel）
- 内核接口被称为系统调用（system call）
### 登录
- /ect/password 中查看登录项。
    zj:x:1000:1000:zj,,,:/home/zj:/bin/bash
    依次是登录名:加密口令:数字用户ID:数字组ID:注释字段:起始目录:shell程序
### 文件和目录
- ['ls'命令的简单实现](https://github.com/zj0395/myCode/blob/master/APUE/1-3_ls.c)
- 文件描述符（file descriptor）通常是一个小的非负整数，内核用以标识一个特定进程正在访问的文件
- 每一个新程序运行时，shell都会为其打开 standard input, standard output, standard error
- 函数 open、read、write、lseek 以及 close 提供了不带缓冲的I/O
- 标准 I/O 函数为不带缓冲的 I/O 函数提供了一个带缓冲的接口
### 程序和进程
- 程序是存储在磁盘上某目录下的可执行文件
- 程序的执行实例被成为进程（process）
- UNIX 系统保证每个系统都有一个唯一的进程ID。总是一个非负整数
- 有三个进程控制函数: fork、exec 和 waitpid
- 一个进程内的所有线程共享同一地址空间、文件描述符、栈以及与进程相关的属性
- **每个线程也拥有各自的栈**
### 出错处理
- Linux 中，出错常量在 errno(3) 手册页中列出
- errno 需注意，仅当函数返回值指明出错时，才去校验 errno；任何函数都不会将 errno 值设为0
- `char* strerror(int errnum)` 和 `void perror(const char* msg)` 用于输出出错消息
- 对于非致命性错误，可使用指数补偿法，延长一段时间
### 用户标识
- 用户ID为0的是root或superuser
- 组文件将用户映射到不同的组ID。组文件通常是 /etc/group
### 信号
- 通常有三种处理方式
    1. 忽略信号
    2. 按系统默认方式处理。对于除数0，默认方式是终止进程
    3. 提供一个函数，信号发生时调用，这被称为捕捉该信号
### 时间值
- 类型 time_t 用于保存日历时间，从1970.1.1.0:0:0开始至今的秒数
- 类型 clock_t 保存进程时间，度量 CPU 资源
- 时钟时间：进程运行的时间总量
- 用户 CPU 时间：执行用户指令所用的时间量
- 系统 CPU 时间：执行内核程序所经历的时间
### 系统调用和库函数
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
- 互斥量、读写锁、条件变量、自旋锁、屏障
### 线程创建
- 新创建的线程可以访问进程的地址空间，并且继承调用线程的浮点环境和信号屏蔽量，但是该线程的挂起信号集会被清除
- `pthread_t pthread_self()`获得本线程的id
### 线程终止
- 三种返回方式
    1. 用`return`返回
    2. 被同进程中的其它线程`pthread_cancel`。返回值的指针只想的内存单元被设置为`PTHREAD_CANCELED`
    3. 线程调用`pthread_exit`
- `pthread_cleanup_push` 和 `pthread_cleanup_pop` 用于清理线程，用的栈
    1. 调用 `pthread_exit` 时
    2. 响应取消请求时
    3. 用非零参数调用 `pthread_cleanup_pop` 时
- `pthread_detach` 分离线程，分离后将无法使用`pthread_join`
### 线程同步
- 增量操作通常分解为以下3步
    1. 从内存单元读入寄存器
    2. 在寄存器中对变量做增量操作
    3. 新的值写回内存单元
#### 互斥量
- `pthread_mutex_lock`、`pthread_mutex_trylock`、`pthread_mutex_unlock`
#### 避免死锁
- 仔细控制互斥量加锁的顺序
- 可以先释放占有的锁，过一段时间再试，使用`pthread_mutex_trylock`
- 多线程的软件设计设计需在代码复杂性和性能之间找到正确的平衡。如果锁的粒度太粗，就会出现很多线程阻塞等待相同的锁；如果锁的粒度太细，那么过多的锁开销会使系统性能受到影响，而且代码变得复杂
#### pthread_mutex_timedlock
- 该函数允许设置线程阻塞时间，超时时返回错误码`ETIMEDOUT`
#### 读写锁
- 允许更高的并行性
- 一般实现：当有一个线程试图获取写锁时，读写锁通常会阻塞随后的读锁请求，以避免写锁饿死
- 非常适合于对数据结构读的次数远大于写的情况
- 使用之前必须初始化，释放底层内存之前必须销毁
- `pthread_rwlock_t`、`pthread_rwlock_init`、`pthread_rwlock_destory`
- `pthread_rwlock_rdlock`、`pthread_rwlock_wrlock`、`pthread_rwlock_unlock`
- 需检查`pthread_rwlock_unlock`的返回值
#### 带超时的读写锁
- `pthread_rwlock_timedrdlock`、`pthread_rwlock_timedwrlock`
#### 条件变量
- `pthread_cond_t`
- 作用：阻塞，直到某信号的发生
- 条件本身是由互斥量保护的：调用前手动获取锁，阻塞时会自动释放锁，醒来后自动获取锁
- 示例代码：**注意，线程醒来，发现队列为空(被其它线程处理了)，就继续等待；如果代码不能容忍这种竞争，就要在发信号的时候占有互斥量，即`pthread_cond_signal( &qready )`写到`pthread_mutex_unlock( &qlock )`之前，这样只会有一个线程获取到锁，醒来**
```cpp
#include <pthread.h>
struct msg
{
    struct msg* m_next;
};

struct msg *workq;

pthread_cond_t qready = PTHREAD_COND_INITIALIZER;
pthread_mutex_t qlock = PTHREAD_MUTEX_INITIALIZER;

void process_msg(void)
{
    struct msg* mp;

    for(;;)
    {
        pthread_mutex_lock( &qplco );
        while( workq == NULL )
        pthread_cond_wait( &qready, &qlock );
        mp = workq;
        workq = mp->m_next;
        pthread_mutex_unlock( *qlock );
    }
};

void enqueue_msg( struct msg *mp )
{
    pthread_mutex_lock( &qlock );
    mp->next = workq;
    workq = mp;
    pthread_mutex_unlock( &qlock );
    pthread_cond_signal( &qready );
}
```
#### 自旋锁
- 忙等待的一种锁；`pthread_spinlock_t`
- 适用于：锁被持有的时间短，而且线程不希望在重新调度上花费太多的成本
- 自旋锁通常作为底层原语用于实现其他类型的锁，根据它们所基于的系统体系结构，可以通过使用测试并设置指令有效的实现。当然这里说的有效也还是会造成CPU的浪费: 当线程自旋等待锁变为可用时，CPU不能做其他的事情。这也是自旋锁只能够被持有一小段时间的原因
- 自旋锁用在非抢占式内核中时是非常有用的: 除了提供互斥机制以外，它们会阻塞中断，这样中断处理程序就不会让系统陷入死锁状态，因为它需要获取已被加锁的自旋锁
- 用户层，自旋锁并不是非常有用，除非运行在不允许抢占的实时调度类中
- 不要在持有自旋锁的情况下调用可能会陷入休眠状态的函数，其它线程获取锁时浪费CPU资源
#### 屏障（barrier）
- 屏障允许每个线程等待，直到所有合作线程都到达某一点
- `pthread_join`就是一种屏障，允许一个线程等待，直到另一个线程退出
- `pthread_barrier_t`、`pthread_barrier_init`
- 使用`pthread_barrier_wait`来表明线程以完成工作，等待其它线程赶上来
- 计数满足条件时，所有线程都被唤醒；不满足则此线程睡眠
- 第一个调用`pthread_barrier_wait`的线程获得的返回值是`PTHREAD_BARRIER_SERIAL_THREAD`；其他线程看到的返回值是0
## 十二、线程控制
### 线程属性
- `pthread_attr_t`
- 管理这些属性的函数遵循相同的模式
    1. 每个对象与它自己类型的属性对象相关联
    2. 有一个初始化函数，把属性设置为默认值
    3. 有一个销毁属性的函数
    4. 每个属性都有一个从属性对象中获取属性值的函数
    5. 每个属性都有一个设置属性值的函数
- detachstate : 线程的分离状态属性，`PTHREAD_CREATE_DETACHED`和`PTHREAD_CREATE_JOINABLE`
- guardsize : 线程栈末尾的警戒缓冲区大小（字节数）
- stackaddr : 线程栈的最低地址，最低内存地址，并不一定是栈的开始位置，处理器架构可能是从高地址向低地址增长
- stacksize : 线程栈的最小长度（字节数）；进程的虚地址空间大小固定，且所有线程共享，所以线程太多时可能需要减少默认的线程栈大小；也有可能需要很大的栈来完成很深的递归或很多自动变量
### 同步属性
- `pthread_mutexattr_t`
#### 进程共享属性
- 默认不在进程间共享
#### 健壮属性
- 默认是持有互斥量的进程在终止时不需要采取特别的动作
- 可设置为`PTHREAD_MUTEX_ROBUST`，进程终止后，其它进程的线程调用`pthread_mutex_lock`时会返回`EOWNERDEAD`；这时候需要调用`pthread_mutex_consistent`来恢复该锁的一致性，再解锁；否则该互斥量将不再可用
#### 类型属性
- `PTHREAD_MUTEX_NORMAL` : 无错误检查和死锁检测
- `PTHREAD_MUTEX_ERRORCHECK` : 错误检查
- `PTHREAD_MUTEX_RECURSIVE` : 允许同一线程内进行多次加锁，内部维护了每个线程的加锁计数
- `PTHREAD_MUTEX_DEFAULT` : 取决于操作系统将其映射为何种类型
- 各类型的错误处理

| 互斥量类型 | 未解锁时重新加锁 | 不占用时解锁 | 已解锁时解锁 |
| :--:       | :--:             | :--:         | :--:         |
| NORMAL     | 死锁             | 未定义       | 未定义       |
| ERRORCHECK | 返回错误         | 返回错误     | 返回错误     |
| RECURSIVE  | 允许             | 返回错误     | 返回错误     |
| DEFAULT    | 未定义           | 未定义       | 未定义       |

- 递归锁可能很难处理，应该只在没有其它可行方案时才使用
> 如果在最早定义数据结构时，预留了足够的可填充字段，允许把某些填充字段替换成互斥量，这种方法也是可行的。不过遗憾的是，大多数程序员并不善于预测未来，所以这并不是普遍可行的实践。
#### 读写锁属性
- 只有一个进程共享属性 : 与互斥量的线程共享属性相同
- 不同平台的实现也可以定义其它属性，但不据具移植性
#### 条件变量属性
- 进程共享属性
- 时钟属性，控制超时函数`pthread_cond_timewait`的时钟类型
- 奇怪的是，Singel UNIX Speciafication 并没有为其它有超时等待函数的属性对象定义时钟属性
#### 屏障属性
- 只有进程共享属性
