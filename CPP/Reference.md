# CPP备忘录

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
