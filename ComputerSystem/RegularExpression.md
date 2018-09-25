# 正则表达式
参考于[正则表达式](https://github.com/ziishaned/learn-regex)
>有些人面临一个问题时会想：“我知道，可以使用正则表达式来解决这个问题”。于是现在他们有两个问题了。——Jamie Zawinski

##### 通配符 '.'
	'.ython'可匹配'python'与'Aython'等
##### 字符集
	'[a-z0-9A-Z]'可匹配所有字母和数字
##### 反转字符 '^'
	'[^a]'匹配除'a'以外的所有字符
##### 选择符号 '|'
	'p(ython|erl)'匹配'python'与'perl'
    ()内的内容成为子模式
##### 可选项 '?'
	'(http://)?(www|.)?python\.org'可匹配'www'或符号、可有可无'http:://'的字符串
##### 重复子模式
	'w*\.python\.org' 可匹配0个或多个w
    'w+\.python\.org' 可匹配1个或多个w
    'w{3,5}\.python\.org' 可匹配3-5个w
##### 字符串的开始与结尾
	'^ht+p' 只匹配开头是http，可以有1个t或多个t
    'org$' 只匹配结尾是org的