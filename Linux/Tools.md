- pwd - print working directory
- hostname - 计算机在网络中的名字
- cd - change directory
- pushd - push directory
- popd - pop diectory
- echo -n 不在最后加换行符，-e使用转义字符，二者都只在bash中
- cut 处理多空格有不足
	- -b 字节
	- -c 字符
	- -f 域，第几个，与-d配合使用
	- -d 自定义分隔符，分成几个域
	- -n 取消分割多字符字符，与b一起
- which 查看可执行文件的位置
- whereis 搜索程序名字。不是实时更新的
- locate 在数据库中查找文件。由系统维护，非实时更新
- seq 生成序列
	- seq 10 → 1...10
	- seq 1 3 10 → 1,4,7,10
	- seq 1 10 → 1...10
	- seq -s "a" 1 10→结果为1a2a...9a10。指定了分隔符
	- seq -w 使所有字符宽度一样
- export - 导出/设定一个新的环境变量
	- 只在当前shell下有效
	- 更有效的方法是在~/.bashrc下添加
- less - 逐页浏览文件
- nl 打印文件和行号
	- -b 后跟 a 表示空行也输出行号， t 表示空行不输出行号
	- -n 后跟 ln 表示最左方，rn 为单元栏内最右， rz 最右同时前面加0
	- -w< n> 后跟数字表示宽度
- cat
	- -b 对非空行编号
	- -n 对所有行编号
- head 
	- -q 隐藏文件名
	- -v 显示文件名
	- -c < n> 显示n个字节
	- -n < n> 显示n行
- tail
	- -f 循环读取，会不断更新
	- -q、-v 与head相同
	- -c、-n 与head相同
	- -s 与-f一起使用，表示两次读取的间隔
- xargs - 用于管道中处理参数
	- -n< n1> 多行输出，把 n1 行看做一行
	- -d'\n' 自定义分隔符
	- -i 可以用{} 表示前面的参数
	- -I 指定一个符号代替{}
	- -p 提示是否连续执行


- find \[path] \[options] \[test] \[actions]
	- options 
		- depth: 先对子目录执行查找
		- -maxdepth <n>最大深度
		- -mindepth <n>最小深度
		- -mount

	- test
		- -name file
		- -iname 忽略名字的大小写
		- -type d为目录，f为普通文件，b为块
		- -atime 最后访问时间 +n表示n天以前，-n表示n天以内
		- -ctime 最后变更时间，mv、chmod都 在此列
		- -mtime 最后内容修改时间
		- 可以使用逻辑运算符 ！、-a=-and、-o=-or
	- actions
		- print

- grep [options] PATTERN [file]
	- options:
		- -c 行数
		- -E 扩展为正则表达式
		- -h 不在前面显示文件名
		- -i 忽略大小写
		- -l 只输出文件名
		- -v 取反
		- -n 行号
		- -r 递归查找目录
		- -q 不显示任何信息
- tr 
	- -s 删除重复的
	- -d 删除
	- -c 对第一个条件取反


- md5sum 生成MD5
- env - 查看你的环境
- man - 阅读手册
- apropos - 寻找手册页面
- diff
	- 使用 -c、-u而不是-e
- patch
	- 与diff可以配合使用
- cron
- ps
	- -A 列出所有执行中进程
	- -w 加宽显示更多信息
	- -au 显示较详细的资讯
	- -aux 显示所有进程，包含其它使用者
	- a 显示现在终端机上所有程序，包括其它用户的
	- c 只包含命令，而不是路径
	- e 包含使用的环境变量
	- f 用ASCII字符显示树状结构，表示程序间的相互关系
	- -H 与 f 类似，但不用 ASCII 码显示
	- -l 显示nice值，即优先级
	- -N 显示所有程序，除了执行ps的程序
	- 会把僵死的进程描述为`z`(zombie)
- bc - 计算器

		ibase=10//输入
        obase=2//输入
        24//输入
        11000//输出


- tr
- sort
- lpr - line printer 按行打印
	- `lpr -P printer file1 file2`


- du - disk usage 查看目录大小
	- -h 使用KB、MB、GB等人类能理解的单位
	- -s 只展示目录的，不展示子目录
	- -b、-k、-m指定使用B、KB、MB


- df - 查看磁盘大小
	- -h 同上
	- -i 显示i节点的数量
- size - 显示可执行文件的大小
``` shell
zj@HelloWorld:~/myCpp$ size SudoKu 
   text	   data	    bss	    dec	    hex	filename
  79075	   2384	    528	  81987	  14043	SudoKu
  ```
  text表示正文段大小，data表示包含静态变量和已经初始化的全局变量的数据量大小，bss通常是指用来存放程序中未初始化的全局变量的一块内存区域。text+data+bss = dec, hex 是dec的16进制表示
