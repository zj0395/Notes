# AWK命令详解
- gawk - pattern scanning and text processing language
- 一行一行处理
## 摘要
- gawk [ POSIX or GNU style options ] -f program-file [ -- ] file ...
- gawk [ POSIX or GNU style options ] [ -- ] program-text file ...
## 选项格式
- POSIX 风格，'-' 开头
- GNU 风格，'--' 开头
- 每个 GNU 长选项都有一个对应的 POSIX 短选项
## 常用选项
- -f program-file || --f program-file
    从 program-file 中读取脚本
- -F fs || --field-separator fs
    用 fs 作为输入的分隔，默认为空格
- -v var=val || --assign var=val
    变量赋值，通常用在 BEGIN 中
- -e program-text || --source program-text
    使用 program-text 作为awk 执行代码，可以通过 -f 引入函数定义，--source 来执行函数调用
- -i include-file || --include include-file
    从 AWKPATH 环境变量的目录中查找，只会被载入一次
## 执行程序
- AWK 程序可以有多个 pattern-action 语句，也可定义函数
'''
              @include "filename"
              @load "filename"
              pattern   { action statements }
              function name(parameter list) { statements }
'''
- Gawk 会读取 program-file, program-text, 或者命令行参数中第一个非选项的语句
- -f 以及 --source 可以使用多次
- @include 开头的可将其它代码文件包含进来，使库的使用更方便。等价于 -i
### 执行顺序
1. 首先，执行 -v 选项的参数赋值；
2. 其次 gawk 编译程序；
3. 之后执行 BEGIN 中的语句（如果有）；
4. 之后处理 ARGV 中的每个文件名，无文件名的话就读取标准输入;
5. 对于每个输入文件，如果存在 BEGINFILE 规则，就会先处理相应代码，文件处理完成后也会处理 ENDFILE 中的代码；
6. 对于每个输入，gawk 都会测试是否符合 AWK 程序中的 pattern, 并执行相应代码；pattern 按照声明顺序来测试；
7. 最后，执行 END 中的代码。
### 对目录的处理
- 根据 POSIX，传入的文件名必须是 text 文件，否则是未定义的行为
- 大部分的 awk 版本都认为目录的传入是致命错误
- gawk 的4.0版本后，会对传入的目录会提供警告，并跳过
## VARIABLES, RECORDS AND FIELD
### Variables
- awk 的变量是动态的，第一次使用产生
- 变量可以使浮点数、字符串，或者都是，取决于使用
- 也有一维数组，可模拟多维数组
### Records
- 正常情况下每一条记录是通过换行符来分割的，可以通过内置变量 `RS` 来改变
- RS 是单字符，或者是正则表达式
- 在 ccompatibilit 模式下，只有字符串的第一个字符用来分割records
- RS 设置为空时，就根据空行来分隔，且不会收到 FS 设置的影响
### Fields
- 每一套 record 读入时，gawk 用 FS 作为分割符将其拆分为 fileds
- FS 可以是单个字符
- FS 为空时，每个字符都成为了一个 field
- FS 也可以是正则表达式
- FS 是单个空格时，空格 and/or tab and/or 换行符 都是有效的分隔符号
- IGNORECASE 值会在 FS、RS 是正则表达式时对其产生影响
- FIELDWIDTHS 变量有效时（空格分割的多列数字），FS 会被忽略，重新设置 FS 会覆盖掉 FILEDWIDTHS
- 每个 field 都可以通过位置变量被引用：$1, $2 ..., $0 代表整个 record
- fields 不一定被常量引用
        n = 5
        print $n
    输出第5个 field
- 变量 NF 可获得 record 中的 fields 数量
- 引用 $(NF+1) 会获得空字符串，$(NF+2)=5 会使 NF 增加，中间的填充为空字符，$0 也会被重新计算
- 负数会产生致命错误，如 $-1
- 降低 NF 的值会导致后面的值丢失，也会重新计算 $0 的值
- 每个 field 改变时会影响 $0, $0 的改变也会同时改变每个 field
### Built-in Variables
- ARGC      命令行参数个数，不包括选项，也不包括program source
- ARGIND    正在处理的参数的 index
- ARGV      命令行数组，可通过索引来获取，也可动态的改变内容
- BINMODE   
- CONVFMT   数字的转换格式，默认是"%.6g"
- ENVIRON   包含现在环境变量的一个数组，如 ENVIRON["HOME"]
- ERRNO     getline、close 发生错误时，信息的字符串
- FIELDWIDTH 每个 Filed 的宽度，前面已描述与 FS 的关系
- FILENAME  当前正处理的文件名，参数中无文件名时，值是"-"。在 BEGIN 中， FILENAME 未定义
- FNR       当前文件中目前为止 record 的个数
- FPAT      
- FS        field 的分隔
- FUNCTAB   
- IGNORECASE 
- LINT      
- NF        当前 record 中 field 的个数
- NR        目前为止 records 总个数
- OFMT      数字的输出格式，默认是"%.6g"
- OFS       输出 field 间隔，默认一个空格
- ORS       输出的 record 间隔，默认换行
- PREC      
- PROCINFO  
- ROUNDMOE  
- RS        输入的 record 分隔，默认换行
- RT        输入的终止符
- RSTART 
- RLENGTH   
- SUBSEP    下标的分割符，默认是"\034"
- SYMTAB    全局变量的数组
            foo = 5
            SYMTAB["foo"] = 4
            print foo    # prints 4
- TEXTDOMAIN
### Arrays
- 

