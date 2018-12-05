### GDB
- 调试core文件 `gdb main core`
- 查看coredump文件名格式 `cat  /proc/sys/kernel/core_pattern`
- 可以产生coredump且不受大小限制，非永久生效 `ulimit -cunlimited`
- 远程调试时，设置符号表 `set solib-search-path /home/zj/zj/linux-sdk/embedded_develop/build_output/xiaodubox/BDSpeechSDK/lib:/home/zj/zj/linux-sdk/embedded_develop/build_output/xiaodubox/BDSpeechSDK/extern/lib` 
