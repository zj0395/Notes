# 装载时链接与运行时链接
- 两种库都为 .so, 生成方式完全一致, gcc -fPic -shared name.o -o name.so
- 装载时链接为普通的，打开时就需要 .so 库, 链接所有 .o 文件生成可执行文件时也需要提供所需的 .so 库
- 运行时链接是通过函数 dlopen() 实现的, 头文件 < dlfcn.h >中, 链接可执行文件时不需要提供库名, 但需要选项 -ldl
```c
#include <dlfcn.h>
void * dlopen(const char* filename, int flags);
int dlclose(void *handle);
void *dlsym(void *handle, const char* symbol);//查找函数名symbol, 返回函数指针
```

