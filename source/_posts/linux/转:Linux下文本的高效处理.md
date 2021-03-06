title: 转:Linux下文本的高效处理
date: 2013-07-27 12:11:24
categories: linux
tags: [shell, linux]
---

原文来源：[http://ahei.info/text-processing.htm](http://ahei.info/text-processing.htm)

``````
    1 引言
    2 关于输入
    3 文本输出
        3.1 整个输出
        3.2 部分输出
    4 文本搜索
        4.1 grep (Global Regular Expression Print)
        4.2 fgrep
        4.3 egrep
        4.4 rgrep
        4.5 agrep (approximate grep)
        4.6 zgrep
        4.7 sgrep (structured grep)
        4.8 nrgrep (Nondeterministic Reverse grep)
    5 文本摘要
        5.1 wc (word count)
        5.2 md5sum
    6 排序去重
        6.1 sort
        6.2 tsort (topological sort)
        6.3 uniq
        6.4 comm
        6.5 shuf (shuffle)
    7 操作字段
        7.1 cut
        7.2 paste
        7.3 join
    8 操作字符
        8.1 tr (translate)
        8.2 expand
        8.3 unexpand
        8.4 colrm (COLumn ReMove)
    9 文本切割
        9.1 split
        9.2 csplit (context split)
    10 文本编码
        10.1 iconv
        10.2 enca
    11 文本格式化
        11.1 column
        11.2 fold
        11.3 fmt
    12 微语言
        12.1 sed (Stream EDitor)
        12.2 awk (Alfred V. Aho, Peter J. Weinberger, Brian W. Kernighan.)
    13 语言
    14 实例
        14.1 我的正则会数学
    15 参考文献
``````

<!--more-->


# 1 引言

所谓的文本处理是指对文本进行查找、替换、删除、排序等操作, linux在文本处理方面提供了大量优秀的工具, 使得在linux下进行文本处理极其的方便.

我们平常的工作中, 经常会用到文本处理, 比如日志分析, 比如文本抽取, 等等, 所以掌握好文本处理, 将会对我们的工作起到极大的作用.

下面我就来逐个介绍下这些强大的工具, 对于我觉得大家可能比较熟知的工具及用法, 我会略过, 或者粗讲下.

# 2 关于输入

Linux哲学中, 为了更好的组合各种命令达到更加强大的功能, 大多数文本处理命令的输入既可以是文件, 也可以是标准输入, 如果没有指定输入文件, 则默认从标准输入读数据. 输出都是标准输出, 方面传给管道线的下一个命令, 想要输出到文件的话, 重定向下即可. 下面介绍的这些命令, 如无特殊说明, 则都可以从文件或者标准输入读入数据.

# 3 文本输出

## 3.1 整个输出

### echo

>输入为命令行参数

非常常用的命令, 主要用作输出字符串. 如果只是为了向管道线的下一个命令传输入的话, 可以使用Here String:

	echo xxx | md5sum
	md5sum <<< xxx

后者速度上应该会快一点, 不需要经过管道.

* -n
不输出换行符(默认输出换行符)
* -e
解释转义符, 常用的转义符 \t, \n; 这个命令还有个最常用的用途就是输出ANSI颜色:
``echo -e '\033[1;31mHello, \033[0m\033[1;33mworld!\033[0m'``
* -E
不解释转义符

### printf

>输入为命令行参数

更强大的输出你想要的文本的命令, 类似C里面的printf

``printf '\033[1;31m%s, \033[0m\033[1;33m%d\033[0m and \u4e2d\u6587!\n' "Hello" 34``

不过此命令较echo来说, 使用率会低很多, 大多数情况下echo就能搞定了.

### yes [STRING]

>输入为命令行参数

不停的输出字符串STRING, 默认是y. 这个命令用处比较少, 但会有用, 比如测试tail命令.

### cat (concatenate)

此命令最大的用途应该就是显示特殊字符了, 如果你告诉我你经常用它给管道线的下一个命令传输入的话, 那么就太浪费了:

	cat file | grep xxx
	grep xxx file

前者相比后者多启动了一个进程, 还经过了管道. file很大的话, 性能差距很容易就看出来了.

* -E
在行尾显示$, 有时候行尾是空格或者TAB, 看不清行尾是哪里, 加了这个选项就知道了
* -T
输出的时候用^I代替TAB符号, 这样很容易知道那一坨空白到底谁是TAB了
* -v
用^和M-表示法输出不可打印字符
``taoshanwen@taoshanwen-laptop ~$ echo -e '\r' | cat -v``
* -e
相当于-vE
* -t
相当于-vT
* -A
相当于-vET, 一般这个选项用的最多
* -n
显示行号, 应该有同学感兴趣
* -b
只对非空行显示行号, 有人感兴趣吗?
* -s
对连续的空行只输出一个空行, 见过好多同学有这样的需求

### tac

看名字知道了, 它和cat是反的, 倒着输出, 先输出最后一行, 接着是倒数第二行, 最后输出的是第一行

* -s, --separator=STRING
设定分隔符, 代替\n分割文本

### rev (reverse)

这个命令和cat也是反的, 不过它不像tac那样, 它输出行的顺序和cat也是一样的, 不过输出每行的时候, 先输出最后一个字符, 接着是倒数第二个字符, 最后才是第一个字符, 下面这个命令可以把输入全部倒过来:

tac | rev

### nl (number lines)

更强大的行号显示工具, 可以控制行号的格式, 宽度. 没有特殊的需求, 用cat -n就够了.

## 3.2 部分输出

### head

只显示文本的开头几行, 比如head -2只显示前面2行

### tail

这个命令相对head来说, 最常用的用途就是不停的打印文件的最新内容了(tail -f)

* -n, -K, --lines=K
显示尾部K行, -n +K显示第K行到文本尾部的所有内容
* -f, --follow[={name|descriptor}]
这个选项表示如果文件尾部有新数据追加进来, 也会显示出来. 这个选项可以根据文件名(name)和文件描述符(descriptor)来监视文件是否有更新. 默认是descriptor.
* --retry
当文件不可访问时, 进行重试, 这个选项和--follow=name组合起来比较有用
* -F
相当于--follow=name --retry

# 4 文本搜索

## 4.1 grep (Global Regular Expression Print)

非常常用的命令, 打印文本中匹配模式的行, 下面的选项最好都能掌握.

>grep [OPTIONS] PATTERN [FILE...]
>grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]

* -E, --extended-regexp

使用扩展正则表达式(ERE), 默认的是基础正则表达式(BRE), BRE中元字符?, +, {, |, (, )失去特殊意义(你是否遇到grep "a|b"是否不能打印出含有a或者b的行?), 想要表达特殊意义的话, 需要用转义字符进行转义(\), \?, \+等

* -F, --fixed-strings

把pattern当作一个固定的字符串, 不进行正则解析. 当你要搜索非正则的固定字符串时(还可能含有正则元字符), 这个选项会非常有用, 而且由于不需要解析正则, 速度会快些.

* -P, --perl-regexp

把pattern解析为perl的正则(PCRE), 由于perl的正则强大而简洁, 所以可以多尝试着使用这个选项.

* -i, --ignore-case

搜索pattern的时候, 忽略大小写. 如果没有这个选项, 可以使用PCRE, 比如:
``grep -P "(?i)AB"``

* -v, --invert-match

显示不能匹配pattern的行

* -e pattern

如果你想要指定多个搜索pattern, 或者你想要搜索的pattern由减号(-)开头(如果直接grep pattern的话, 会被解析为grep的选项), 就可以用这个选项了.

* -f FILE, --file=FILE

从文件中获取pattern, 每行一个pattern

* -x, --line-regexp

pattern必须要匹配整行, 这个选项等价于 "^pattern$"

* -w, --word-regexp

pattern必须要匹配整个单词, 这个选项等价于 "\bpattern\b"

* -c, --count

不打印匹配的行, 只打印匹配的行数, 等价于``grep pattern | wc -l``

* --color[=WHEN], --colour[=WHEN]

用颜色高亮出匹配的

* -m NUM, --max-count=NUM

当发现NUM个匹配行后, 停止扫描剩下的文本.

* -q, --quiet, --silent

不打印任何信息, 发现匹配即退出, 并返回0, 否则返回1. 我们经常只是想查看整个文本里面是否有匹配, 这时候这个选项就非常有用了, 速度会快很多.

* -o, --only-matching

只显示匹配pattern的字符串, 匹配行的其余部分不显示

* -n, --line-number

在匹配行前面打印行号

有时候想看看匹配行周围都是啥, 下面这几个选项就非常有用了:

* -A NUM, --after-context=NUM

打印匹配行的后面NUM行

* -B NUM, --before-context=NUM

打印匹配行的前面NUM行

* -C NUM, -NUM, --context=NUM

打印匹配行的周围NUM行

* -a, --text

有时候文件中含有一些非可打印字符, grep可能会把它识别成二进制文件, 这时候grep只会打印出是否匹配pattern的信息, 并不会打印匹配的每行, 这个选项会强制grep把该文件当文本文件处理

* -R, -r, --recursive

递归处理文件夹下的所有文件

* -l, --files-with-matches

不打印匹配的行, 只打印匹配的文件

* -L, --files-without-match

和-l相反, 不打印匹配的行, 只打印不匹配的文件

* -h, --no-filename

搜索多个文件时, 会在每行前面输出文件名, 如果你不喜欢, 使用此选项吧.


## 4.2 fgrep

grep -F

## 4.3 egrep

grep -E

## 4.4 rgrep

grep -r

## 4.5 agrep (approximate grep)

grep的模糊匹配版本

## 4.6 zgrep

对压缩文件进行grep, 接受的选项和grep完全一样

## 4.7 sgrep (structured grep)

对结构化的文本, 如SGML、XML、HTML进行搜索、抽取, 功能非常强大

## 4.8 nrgrep (Nondeterministic Reverse grep)

类似agrep

# 5 文本摘要

## 5.1 wc (word count)

最主要的用途就是统计行数

* -l, --lines

最常用的选项, 统计行数

* -L, --max-line-length

输出文本最长行的长度

* -w, --words

输出单词数

* -m, --chars

输出字符数

* -c, --bytes

输出字节数

## 5.2 md5sum

打印文本的md5, 主要用作文件校验, 防止文件传输时发生错误或者被篡改. -c选项检查md5是否正确

# 6 排序去重

## 6.1 sort

非常常用的命令, 啥序都能排

* -r, --reverse

逆序排序, 默认是按从小到大排, -r后就从大到小了

* -c, --check, --check=diagnose-first

检查输入文件是否是有序的, 不是的话, 会打印哪行开始不是有序的

* -C, --check=quiet, --check=silent

类似-c, 但是不打印错误信息, 只返回错误码1

* -k, --key=POS1[,POS2]

这个应该是sort最nb的地方了, 可以精确控制要排序的对象. POS具备这样的形式:

``F[.C][OPTS]``

其中, F是字段号, C是字符号, OPTS是排序选项, 可以每个字段排序的规则不一样. F, C都是从1开始

	sort -t ' ' -k1,1d -k2.2,2n <<-EOF
	bb 113
	aa 224
	cc 323
	dd 444
	cc 513
	EOF

	aa 224
	bb 113
	cc 513
	cc 323
	dd 444

* -u, --unique

对输出结果进行去重, 只输出重复的记录中的第一条记录

* -m, --merge

对有序的输入文件进行归并, 这个选项使得你能够在多核机器上优化大数据集的排序

* -s, --stable

使得sort成为稳定排序

* -T, --temporary-directory=DIR

设定指定的临时文件夹, 存放中间数据. 当你排序非常大的文件时, 而且/tmp所在的分区空间不够时, 就会用到该选项了

* -n, --numeric-sort

把输入当整数来排序, 可以有负数, 但是不能含有加号(+)的正数, 这种输入用-g搞定吧

* -g, --general-numeric-sort

把输入当作数值来排序, 可以有浮点数. 如果输入是整数的话, 就用-n搞定吧, 人家性能高些.

* -h, --human-numeric-sort

可以排序2K, 1G等带单位的数字, 很爽啊, 想排序某文件夹下所有文件和文件的大小吗:
``du -sh * | sort -h``

* -M, --month-sort

按月份进行排序, ``'JAN'< 'FEB' < … < 'DEC'``

* -d, --dictionary-order

按字典序排序, 忽略字母、数字、空白字符外的所有字符

* -V, --version-sort

你开发的软件有很多版了没? 排下吧, 根据版本号

* -t, --field-separator=SEP

设置字段分隔符, 默认为空白字符. 可惜的是, 这个字段分隔符只能为单个字符

* -b, --ignore-leading-blanks

忽略前导空白字符

* -f, --ignore-case

忽略大小写

* -i, --ignore-nonprinting

忽略不可打印字符

* -R, --random-sort

随机排序, 我想你会用到它的, 反正我用过几次. 不过排序结果不完全随机,
	因为sort会先对每行进行hash, 然后对hash值进行排序,
	所以相同的行一定会排到一块. 不过也许, 这正是你想要的.
		如果你想更乱或者更加强大的功能的话, 看
		``shuf (shuffle) ``

* 陷阱

你是否经常sort一个中文文件却得不到正确结果? 那就对了, 你肯定没设置好语言环境(locale), 试试LC_ALL=C sort吧. sort会根据本地语言环境对输入文本进行排序. LC_ALL=C表示会根据字节值来排序. 或许你说我怎么见到的都是LANG=C sort啊, 来, 我们看看bash info上关于LANG和LC_ALL的解释:

>LANG   Used to determine the locale category for any category not specifically selected with a variable starting with LC_.
>LC_ALL This variable overrides the value of LANG and any other LC_ variable specifying a locale category.
>LC_COLLATE
>	This variable determines the collation order used when sorting the results of pathname expansion, and  determines  the behavior  of  range  expressions,  equivalence  classes, and collating sequences within pathname expansion and pattern matching.

	* LANG
	如果你没有用LC_来设定某个分类的locale, 将会使用LANG来决定这个分类的locale
	* LC_ALL
	该变量会覆盖LANG和LC_
	* LC_COLLATE
	该变量设置排序时的locale

所以, sort时, 设置LC_ALL是最保险的做法.

## 6.2 tsort (topological sort)

拓朴排序, 该命令可能会用的比较少

	$ tsort <<EOF
	a b c
	d
	e f
	b c d e
	EOF

输出:

	a
	b
	c
	d
	e
	f

## 6.3 uniq

也是非常常用的一个命令. 这个命令主要用来对有序序列进行去重, 所以它常和sort联合起来使用, 但是sort -u本身就有去重的功能, 所以当你仅仅只是为了去重时, sort -u就可以帮你搞定了(当输入文本巨大时, 可以用hash来去重提高性能, 比如awk的关联数组), 所以呢, 当年需要对重复的数据进行统计时, 会用到uniq. 当然其实uniq相比sort -u而言, 对重复数据有更加强大的处理

* -c, --count

在每行文本前面输出重复次数

* -d, --repeated

只显示重复的行, 重复的行只显示一行

* -D, --all-repeated[=delimit-method]

显示所有重复的行, 注意该选项与选项-d的区别

* -u, --unique

只打印不重复的行

* -i, --ignore-case

比较的时候不区分大小写

* -f, --skip-fields=N

不比较前面N个字段, 字段分隔符为空白字符

* -s, --skip-chars=N

不比较前面N个字符

* -w, --check-chars=N

每行最多比较前面N个字符

* 实例演示

大家看了uniq上面几个选项后, 是不是有uniq没有太大用处的感觉? 这都是错觉, 下面我给大家演示下uniq在集合运算(统计中有大量的应用)方面巧妙的应用.

	* 并集 ``sort A B | uniq``
	* 交集 ``sort A B | uniq -d``
	* 差集(A-B) ``sort A B B | uniq -u``

* 缺陷
	* 不能控制字段分隔符
	* 不能像sort -k那样精确的控制要比较的对象

## 6.4 comm

逐行比较两个有序文件, 分三列输出文件1独有的行、文件2独有的行、文件12共有的行,

	$ cat ab
	ax
	by
	cz

	$ cat ac
	ax
	bd
	cz

	$ comm ab ac
		ax
	bd
	by
		cz

* -1

不输出第一列(文件1独有的行)

* -2

不输出第二列(文件2独有的行)

* -3

不输出第三列文件3独有的行

* --check-order

检查输入文件是否有序

* --nocheck-order

不检查输入文件是否有序

* --output-delimiter=STR

设定输出分隔符, 默认为TAB

* 实例演示
	* 交集 ``comm -12 <(sort A) <(sort B)``
	* 差集(A-B) ``comm -23 <(sort A) <(sort B)``

## 6.5 shuf (shuffle)

如果``sort -R``产生的结果还不够乱的话, 我想这个命令应该就是你需要的了.
该命令产生完全乱序的结果, 而且速度应该比``sort -R``快(shuf不用排序), 还有功能更强大

* -e, --echo

对命令行参数乱序

* -i, --input-range=LO-HI

对LO到HI之间的数字进行乱序, 比如shuf -i 12-100

# 7 操作字段

## 7.1 cut

挺常用的一个命令, 能非常方便的取某个字段

* -f, --fields=LIST

选择要输出的字段

* -c, --characters=LIST

选择要输出的字符

* -b, --bytes=LIST

选择要输出的字节

* -d, --delimiter=DELIM

设定字段分隔符, 默认是TAB. 可惜的是, 该分隔符也只能是单个字符.

* --complement

取设定的选择LIST的补集

* -s, --only-delimited

忽略不包含分隔符的行, 默认操作是输出整行

* --output-delimiter=STRING

设定输出分隔符

* LIST

* -f, -c, -b选项使用的列表, 可以有下面几种形式:

	* N
	第N个字段/字节/字符
	* N-
	从第N个到最后一个
	* M-N
	从第M个到第N个
	* -M
	从第一个到第N个

列表可以有多个, 之间以逗号分割, 比如:

``cut -f1-3,4-7``

## 7.2 paste

这个命令很有意思, 把两个文件按行粘贴到一块, 曾经我想自己写个程序搞定这个需求, 后来发现linux下竟然已经有这玩意了(linux总能给你带来惊喜)

	$ cat num2
	1
	2
	$ cat let3
	a
	b
	c
	$ paste num2 let3
	1       a
	2       b
		c

* -d, --delimiters=LIST

paste两个文件的时候, 默认是用TAB分割, 这个选项设定分隔符, 同为可惜的是, 只能为单个字符(主要是paste可以粘贴多个文件, 这个选项的第二个字符用来分割第二个和第三个文件)

* -s, --serial

默认paste是竖着粘贴的, 加了这个选项后, 就横着粘贴了:

	$ paste -s num2 let3
	1       2
	a       b       c

## 7.3 join

这是一个稍微高级点的命令, 它把输入文件当成一个key/value对, 然后会把同一个key的所有value粘贴到一块, 来个例子:

	$ cat file1
	a 1
	b 2
	c 3
	$ cat file2
	a 4
	c 6
	$ join file1 file2
	a 1 4
	c 3 6

join默认把第一额字段当作key, 字段之间以空格分割, 作为key的字段必须有序.

* -i, --ignore-case

比较字段时, 忽略大小写

* -t CHAR

使用CHAR作为字段分隔符, 又是只能为单个字符(杯具…)

* -1 FIELD

设定第一个文件的key为第FIELD个字段

* -2 FIELD

设定第二个文件的key为第FIELD个字段

* -j FIELD

`` -1 FIELD -2 FIELD``

* -a FILENUM

join默认只打印拥有相同key的行, 该选项会打印第FILENUM个文件中没有匹配上的行

* -v FILENUM

和-a选项有点类似, 该选项只打印第FILENUM个文件中没有匹配上的行, 不会打印匹配上的行

* --check-order

检查输入文件作为key的字段是否有序

* --nocheck-order

不检查输入文件作为key的字段是否有序

* -o FIELD-LIST

高级的控制输出对象的选项, FIELD-LIST中的每个元素具有下面这样的形式:

	* 0
	表示做为key的字段
	* M.N
	M为文件号, 取值为0或者1, N为字段号, M.N就是取第M个文件第N个字段

每个元素之间以逗号或者空格分割

* -e EMPTY

 -o选项中, 可能文件M中没有字段号N, 这时候输出的时候用EMPTY代替.

# 8 操作字符

## 8.1 tr (translate)

主要对文本中的字符进行替换、删除.

* 该命令只支持标准输入, 不支持从文件输入.
* tr仅支持单字节字符.

>tr [OPTION]... SET1 [SET2]

字符集合可以由一系列的字符构成, 也可以具有以下形式:

* CHAR1-CHAR2
从CHAR1到CHAR2的所有字符
* [CHAR*]
这种形式只能出现在SET2中, 表示拷贝CHAR直到SET2和SET1的长度相等
* [CHAR*REPEAT]
REPEAT个CHAR
* [:alnum:]
所有的字母和数字
* [:alpha:]
所有的字母
* [=CHAR=]
和CHAR属于同一个字符类中的所有字符

当提供2个字符集合时, 表示把SET1中的字符替换成SET2中的对应的字符, 比如:

	tr a A < file # 把文件file中的小写a都变成大写A
	tr '[:lower:]' '[:upper:]' < file # 把文件file全部大写

* -d, --delete

删除出现在集合1中的所有字符. 下面的命令把文件file中所有的行连成一行:
``tr -d "\r\n" < file``

* -s, --squeeze-repeats

把SET1中连续的字符都替换成1个字符, 当SET2也提供时, 首先执行删除连续字符操作, 然后才执行替换操作

* -c, -C, --complement

使用SET1的补集

* -t, --truncate-set1

首先把SET1中的字符截断到和SET2长度相等

* 陷阱

经常见到有同学对会含有中文的文件用上面的方法进行大小写转换:

	# 终端编码为GB18030编码
	$ tr '[:upper:]' '[:lower:]' <<< 琄
	琸

为什么琄会变成琸呢?

上面我们说到, tr是按字节来处理的, 而GB18030编码第二个字节编码范围为0×40-0×7E和0×80-0×FE, 这样, 第二个字节就可能出现ASCII码, 我们来看下上面2个汉字的GB18030编码值:

	$ od -c  <<< 琄
	0000000 254   K  \n
	0000003
	$ od -t x1  <<< 琄
	0000000 ac 4b 0a
	0000003

	$ od -c  <<< 琸
	0000000 254   k  \n
	0000003
	$ od -t x1  <<< 琸
	0000000 ac 6b 0a
	0000003

看来确实如此, 琄的第二个字节是字符大K, 琸的第二个字节是字符小k.

看来, 如果文本里含有多字节字符, 使用tr的时候得小心咯.

## 8.2 expand

每个编辑器对TAB的显示设置不一样, 有的显示为8个字符, 有的显示为4个字符, 这样就造成了在A编辑器下排版很漂亮, 到了B编辑器下变得一团糟, 所以编码的时候最好使用空白字符代替TAB(Emacs中这样设置: (setq-default indent-tabs-mode nil), :) ), expand命令也可以帮你把TAB转换成空格

* -i, --initial

不转换非空白字符后的TAB

* -t, --tabs=NUMBER

设置一个TAB转换成几个空格, 默认是8

## 8.3 unexpand

Makefile缩进的时候, 必须是TAB, 所以有时候又需要把空格变成TAB, 就靠unexpand了

* -a, --all

转换所有的空格, 默认情况下只转换开头的空格

* --first-only

只转换开头的空格

* -t, --tabs=N

几个空格转换成一个TAB, 默认是8

## 8.4 colrm (COLumn ReMove)

> colrm [start [stop]]

该命令只支持标准输入, 不支持从文件输入.

删除每行从start到stop之间的字符, 如果stop没有指定的话, 则删除到末尾. 需要注意的是, TAB被认为占8列(不知道为啥这样搞)

# 9 文本切割

## 9.1 split

切割文本INPUT成文件PREFIXaa, PREFIXab … 默认每个文件1000行, PREFIX为x

> split [OPTION]... [INPUT [PREFIX]]

* -l, --lines=NUMBER

按行切割, 每个输出文件NUMBER行, 比较常用的切割方式

* -b, --bytes=SIZE

按字节切分

* -C, --line-bytes=SIZE

每个文件最多SIZE个自己, 但是每行都完整的保存到一个输出文件中, 即不像-b那样, 可能一个整行被拆分到多个文件中去

* -d, --numeric-suffixes

使用数字做为后缀名

## 9.2 csplit (context split)

根据模式切割文件, 简单了解即可

> csplit [OPTION]... FILE PATTERN...

# 10 文本编码

## 10.1 iconv

经常会用到, 主要用来转换编码

* --list, -l

列出可以识别的编码

* -c

转换的时候, 忽视无效的字符, 如果没有加这个选项, iconv碰到这个无效字符会直接报错退出, 所以最好加上这个选项

## 10.2 enca

我们经常需要知道文件的编码, 这个命令帮你搞定

# 11 文本格式化

打扮一下你的文本吧.

## 11.1 column

按列漂亮的输出:

``$ (printf "PERM LINKS OWNER GROUP SIZE DAY HH:MM NAME\n"; ls -l | sed 1d) | column -t``

## 11.2 fold

将一个比较长的文本行输出进行"折行".

## 11.3 fmt

将输入按照指定宽度进行折行, 功能较fold强大些

# 12 微语言

下面介绍文本处理中两个最强大的命令sed和awk, 它们已经具有一些程序设计语言的特征了, 特别是awk, 所以, 我们的脚本中, 放眼望去, 皆是awk阿. 熟练掌握这两个命令, 你的文本处理功力将会极大的提升阿.

## 12.1 sed (Stream EDitor)

sed是一个流编辑器, 类似ed(行编辑器, 通过各种命令编辑文件), 它提供了各种替换、删除的命令, 使得这些编辑操作能自动化起来.

### 工作流程

sed维护2快内存(也可以理解为2个变量, 或者说是2个寄存器), 分别叫做 pattern space 和 hold space, sed对每行输入执行下面的循环:

1. 读入输入行, 去掉尾部的换行符, 存入pattern space
2. 执行sed命令, 每条sed命令都可以有个地址与它关联, 这个地址就类似于条件语句, 只有这个条件语句通过验证时, 其对应的命令才会执行
3. 执行完所有的sed命令后, 如果没有指定sed的-n选项, 将会打印pattern space的内容, 然后再输出换行符. 最后继续读入下一行, 进行下一次的循环

每次循环开始时, pattern space的内容会被清空, hold space则不会

### 地址格式

sed地址可以具有以下的形式:

	* NUMBER
	指定执行命令的行号, 只有在这行, 对应的命令才会被执行, 行号从1开始, 另外, 如果没有指定-i或者-s选项的话, 所有的输入文件会被当成一个输入流, 行号就会一直累加的
	* FIRST~STEP
	在FIRST、FIRST+STEP、FIRST+2*STEP、、FIRST+3*STEP行执行对应的命令
	* $
	最后一行
	* REGEXP
	在匹配上正则REGEXP的行执行对应的命令, 如果REGEXP中含有/, 需要用\转义
	* \%REGEXP%
	在匹配上正则REGEXP的行执行对应的命令, %也可以是其他字符, 如果REGEXP中含有%, 需要用\转义
	* /REGEXP/I, \%REGEXP%I
	忽略大小写
	* /REGEXP/M, \%REGEXP%M
	可以匹配多行, M表示multi-line

如果没有指定地址的话, 表示所有行对执行命令. 还可以提供2个地址, 指定一个地址范围, 这2个地址之间以逗号分割, 比如:
``ADDRESS1,ADDRESS2``

这样, 第一次匹配上ADDRESS1的行与第一次匹配上ADDRESS2的行之间的所有行都会执行对应的命令.

GNU sed还支持下面几种地址范围:

	* ADDR1,+N
	匹配ADDR1, 以及接下来的N行
	* ADDR1,~N
	匹配ADDR1, 直到行号为N倍数的行

在地址或者地址范围后加感叹号(!), 表示取反.

### 常用命令

由于sed默认会打印pattern space, 所以不加任何命令的话, 就和cat一样, 打印所有的输出:

	$ cat ab
	ab
	ab
	ac
	ad
	ae
	ac
	ab

	$ sed "" ab
	ab
	ab
	ac
	ad
	ae
	ac
	ab

* d

删除pattern space, 立即进入下一轮循环.

ls输出的时候, 第一行有个摘要, 如果不想显示的话, 这样:

``ls -l | sed 1d``

* p

打印pattern space.

输出文件ab第5到第10行:

``sed -n 5,10p ab``

* q [EXIT-CODE]

立即以返回码EXIT-CODE(默认为0)退出sed, 如果没有加-n选项的话, 当前的模式空间也会打印出来.

如果文件很大的话, 下面的方法输出文件ab第5到第10行会快很多:

``sed -n "5,10p; 10q" ab``

* n

如果没有加-n的话, 打印模式空间, 然后直接进入下一轮循环.

不打印第18行:

``sed -n "p; 18n"``

* { COMMANDS }

一组命令, 这组命令共用同一个地址.

打印第8行:

``sed -n "8 {p; q}"``

* s/REGEXP/REPLACEMENT/FLAGS

这个命令估计是大家用的最多的命令了. 前面几个命令大家不知道的情况下, 可能通过其他命令解决了, 但是这个命令的功能除了awk, 其他的做不了, 而sed比awk更简洁.

这个命令主要是对pattern space进行替换, 对匹配REGEXP的部分用REPLACEMENT进行替换, 用来分割的/可以由其他字符组成, 比如s:REGEXP:REPLACEMENT:FLAGS. REPLACEMENT可以由原始的字符组成, 也可以由下面带有特殊意义的串组成:

	* &
	匹配REGEXP的部分

	* \d
	d为1-9的数字, \d表示REGEXP中第d个括号匹配的部分, 比如REGEXP为:
	``a(..(..))``
	pattern space为abcde, 那么\1为bcde, \2为de

	* \L
	把REPLACEMENT中的字符变成小写, 直到遇到\U和\E. 比如:

		$ cat ab
		AB
		AB
		ac
		ad
		ae
		ac
		AB

		$ sed -r 's/(AB)/\L\1YYY/' ab
		abyyy
		abyyy
		ac
		ad
		ae
		ac
		abyyy

	上例中, 本来\1应该是AB, 但是\L把它全变成小写了. 而且后面的YYY也变成小写了.
	
	* \l
	把下一个字符变成小写

	* \U
	把REPLACEMENT中的字符变成大写, 直到遇到\L和\E.

	* \u
	把下一个字符变成大写

	* \E
	结束\L和\U的作用

s命令后的FLAGS可以由下面几种:
	
	* g
	s命令默认只替换第一个匹配, g可以让它全部替换
	* NUMBER
	替换第NUMBER个匹配
	* p
	如果发生了替换, 打印模式空间.
	搜索文件ab中xxx并替换成yyy打印出来:
	``sed s/xxx/yyy/p ab``
	* i, I
	正则匹配忽略大小写

* y/SOURCE-CHARS/DEST-CHARS/

类似tr命令, 用DEST-CHARS对应的字符替换出现在SOURCE-CHARS中的字符. 和s命令一样, 分隔符/也可以是其他字符.

* =

打印行号. 下面的命令类似grep -n:

``sed  -n '/xxx/ {=; p}'``

### 高级命令

* h
用pattern space替换hold space
* H
先在hold space追加换行符, 再往hold space追加pattern space
* g
用hold space替换pattern space
* G
先在pattern space追加换行符, 再往pattern space追加hold space
* x
交换pattern space和hold space的内容
* D
删除模式空间的第一行. 如果模式空间中还有内容的话, 开始进入下一轮循环, 但不读入输入. 如果没有内容的话, 读入输入并进行下一轮循环.
* N
追加换行符到pattern space, 并读入下一行输入追加到pattern space, 如果已经没有任何输入, 直接退出sed, 不再处理任何命令.
* P
大写p, 打印pattern space第一行
* z
清空pattern space

### 专家命令

* : LABEL
设定标签, 类似C语言中设定一个标签, 然后可以goto之
* b [LABEL]
跳转到标签, 如果没有提供标签的话, 直接进入下一轮循环
* t [LABEL]
这轮循环中, 如果s命令替换成功过, 则跳转到标签. 如果没有提供标签的话, 直接进入下一轮循环
* T LABEL
和t LABEL相反, s命令替换失败, 才跳转到标签

### 命令选项

* -n, --quiet, --silent
禁止自动打印pattern space
* -i[SUFFIX], --in-place[=SUFFIX]
原地编辑文件, 文本修改后会直接影响到输入文件
* -r, --regexp-extended
启用扩展正则, 默认是基础正则
* -s, --separate
把每个文件当作单独的输入流, 而不是一个输入流

给我一个寄存器, 我可以干好多事

各位看完上面所说的sed命令后, 是不是觉得sed只能做一些替换、删除等操作， 为什么称为微语言呢, 我之所以把它归到微语言这一类是有原因的, 它具有下面几个语言的特征:

1. 有条件判断能力, sed的地址就是一种条件判断, 还有标签命令也是条件判断
2. 有流程控制能力, 标签命令就可以做到
3. 有变量, 虽然很少, 只有2个, 但是仍然可以干好多事了, 看下面的例子

更多的例子参加sed info

* tac

模拟tac
``sed -n '1!G; $p; h'``

为数字字串增加逗号分隔符号， 将1234567变为1,234,567

``sed -r ':a; s/(.*[0-9])([0-9]{3})/\1,\2/; ta' <<< 124523536543652``

## 12.2 awk (Alfred V. Aho, Peter J. Weinberger, Brian W. Kernighan.)

awk是文本处理的利器, 前面那些命令能干的事它都能搞定.

### 工作流程

awk的工作方式有点类似sed, sed是地址+命令, awk则是pattern+action, pattern是要匹配的模式, action是要执行的命令, pattern可以由下面几种形式:

* BEGIN
awk程序开始处理输入时
* END
awk程序结束处理输入时
* BEGINFILE
awk程序开始处理每个文件时
* ENDFILE
awk程序结束处理每个文件时

* regular expression
* relational expression

关系表达式

	* pattern && pattern
	* pattern || pattern
	* pattern ? pattern : pattern
	* (pattern)
	* ! pattern
	* pattern1, pattern2

action要以大括号括起来, 比如:

``awk '0{print} 1{print}' .emacs``

### 内置变量

* ARGC
awk输入参数的个数, 不包括awk自己
* ARGV
命令行参数
* ARGIND
当前处理的文件在ARGV中的位置, ARGV[ARGIND]表示当前正在处理的文件, 可以通过这个变量来对不同的输入文件做不同的处理
* FNR (File Number Record)
当前文件的记录总数
* NR (Number Record)
目前处理的记录总数
* NF (Number of Field)
当前记录的字段数

### 网络编程

awk能开发网络程序, 你相信吗?

	$ cat test.awk
	#!/usr/bin/awk -f

	BEGIN {
		str = "GET /index.html HTTP/1.1\r\nHost: www.baidu.com\r\n\r\n"
		print str |& "/inet/tcp/0/www.baidu.com/80"
		"/inet/tcp/0/www.baidu.com/80" |& getline
		print
	}

	$ awk -f test.awk
	HTTP/1.1 200 OK

* 陷阱

	* tolower/toupper

	和tr一样, 这2个函数也是对字节进行处理

	* 判断元素是否存在

	你是否这样判断某元素是否存在于某数组:

	``if (a[e] != 2) { ... }``

	如果输入很大的话, 过会你就会发现你的awk占了很多内存, 原因就是a[e]的时候, 如果awk发现a中没有e, 就会把e插入到a中, 这样一来内存自然越来越大, 正确的判断方法是:

	``if (!(e in a)) { ... }``

	用过python的朋友可能会这样写:

	``if (e not in a) { ... }``

	很不幸, 没有这样的语法, 而且还不报错, 我猜awk把e not连接成一个字符串了…

# 13 语言

a2p
s2p
perl
python

# 14 实例

## 14.1 我的正则会数学

multi-sort

# 15 参考文献

* 相关命令的info及
 [coreutils](http://www.gnu.org/software/coreutils/manual/coreutils.pdf)
* [高级Bash脚本编程指南: 文本处理命令](http://www.tsnc.edu.cn/default/tsnc_wgrj/doc/abs-3.9.1_cn/html/textproc.html)
* [Survey of Global Regular Expression Print (GREP) Tools](http://www.cosc.brocku.ca/~taa/papers/abou-assaleh_csci6306a.pdf)
