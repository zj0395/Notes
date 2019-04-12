# CPP备忘录

- c++11 保证，多个线程下，局部静态变量也只会被初始化一次 https://stackoverflow.com/questions/8102125/is-local-static-variable-initialization-thread-safe-in-c11
- 模板类的静态变量定义可以直接写在头文件里，运行时即便各个cpp文件都引用了头文件，模板类中的静态变量也只会被初始化一次 https://stackoverflow.com/questions/1553854/template-static-variable
- c++11中，嵌套类自动成为友元类 https://stackoverflow.com/questions/5013717/are-inner-classes-in-c-automatically-friends/14759603#14759603

- `<atomic>`
	- std::atomic_flag c++11原子的布尔类型
	- std::atomic 对数据结构进行原子性封装，不可复制也不可移动


- c++内存模型: http://zh.cppreference.com/w/cpp/language/memory_model
- volitile: http://zh.cppreference.com/w/cpp/language/cv

- `<thread>`
	- std::thread c++11线程类
	- std::this_thread::yield() 避免忙等待
	- std::sleep_for() 按需等待
	- std::chrono c++11时间类


