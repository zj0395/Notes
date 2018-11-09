# Adavance Programming in the UNIX Environment
[书中代码](https://github.com/zj0395/myCode/APUE)
## UNIX 基础知识
#### UNIX 体系结构
- 严格来讲，操作系统可定义为一种软件，控制计算机硬件资源，提供程序运行环境。通常将这种软件称为内核（kernel）
- 内核接口被称为系统调用（system call）
#### 登录
- /ect/password 中查看登录项。
    `zj:x:1000:1000:zj,,,:/home/zj:/bin/bash`
    依次是登录名:加密口令:数字用户ID:数字组ID:注释字段:起始目录:shell程序
#### 文件和目录
- ['ls'命令的简单实现](https://github.com/zj0395/myCode/APUE/ls.cpp)
