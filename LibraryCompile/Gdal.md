# GDAL
## linux下编译
- 可能需要 geos 和 proj 两个库，同样在 gdal 官网下载
- 正常的三条命令 ./configure、make、sudo make install
- 第三条命令需要 root 权限，应为会在 usr/local/ 下生成 bin、lib、include 三个文件夹，里面放着 这三个库的 的可执行文件、依赖的库、依赖的头文件

## windows下编译
- 首先 VS 的目录下有一个工具，vcvars32.bat，可用于设置环境变量，如果提示没有没有 nmake 命令，就可以运行该工具，自动设置环境变量

### nmake /f makefile.vc
- 如果缺少 windows.h 或 stdio.h ，需要设置两个环境变量；即两个 include 目录，一个是 Microsoft SDK 中，另一个是在 VC 中。windows 设置环境变量要用“;”来设置多个。
- 同样，如果提示缺少 rc 命令，也需要设置 Microsoft SDK 上的 bin 的目录作为 Path 环境变量的一个值
- 如果缺少某个 lib 库，也需要设置 Microsof SDK 的 lib 和 VS 的 lib 作为 lib 环境变量

### nmake /f makefile.vc install
### nmake /f makefile.vc devinstall