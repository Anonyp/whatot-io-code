title: 转:Makefile常用信息查询页
date: 2013-09-22 21:34:38
categories: [linux_program]
tags: [Makefile, make, program]
---

From [http://roclinux.cn/?p=3162](http://roclinux.cn/?p=3162)

### 符号说明

**符号** **作用**

* ``\``     换行符
* ``@``     放在命令前面隐藏命令输出
* ``-``     放在命令前面忽略命令错误
* ``:``     以来规则定义符号，即目标:依赖
* ``.PHONY``显式声明伪目标
* ``$@``    表示规则中的目标文件集
* ``$%``    仅当目标是函数库文件时，表示规则中的目标成员名
* ``$>``    依赖目标中的第一个目标名字，如果依赖目标是以模式（%）定义的，那么``$>``将是符合模式的一系列的文件集。注意，其是一个一个取出来的
* ``$?``    所有同目标相比更新的依赖目标的集合。以空格间隔
* ``$^``    所有的依赖目标的集合。以空格间隔，且会去重
* ``$+``    和``$^``作用相似，但不会去重
* ``$*``    表示目标模式中%及其之前的部分
* ``D/F``   表示路径和文件名部分。``$(@F)``表示``$@``的文件部分

<!-- more -->

### 函数说明

**函数** **说明**

* ``$(subst <from>, <to>, <text>)``把``text``中的``from``替换为``to``
* ``$(patsubst <pattern>,<replacement>,<text>)``模式替换
* ``$(strip <string>)``去掉``string``字串中开头和结尾的空字符
* ``$(findstring <find>,<in>)``在``in``中找``find``字串
* ``$(filter <pattern>,<text>)``过滤``text``，并保留符合``pattern``模式的字符串
* ``$(filter-out <pattern>,<text>)``和``filter``相反
* ``$(sort <list>)``给字串``list``中的单词升序排序
* ``$(word <n>,<text>)``取字串``<text>``中第n个单词
* ``$(wordlist <s>,<e>,<text>)``从``text``中取从``s``开始到``e``的字符串。``s``和``e``是数字
* ``$(words <text>)``统计``text``的单词个数
* ``$(firstword <text>)``取``text``的第一个单词
* ``$(dir <names>)``从文件名``names``中取出目录部分
* ``$(notdir <names>)``从文件名``names``种取出非目录部分
* ``$(suffix <names>)``取出文件名后缀
* ``$(basename <names>)``取出文件名部分
* ``$(addsuffix <suffix>,<names>)``把后缀``suffix``加到``names``中每个单词后面
* ``$(addprefix <prefix>,<names>)``把前缀``prefix``加到``names``每个单词前面
* ``$(join <list1>,<list2>)``把``list1``加到``list2``后面
* ``$(foreach <var>,<list>,<text>)``把``list``里的单词一一取出，放到``var``中，然后执行``text``所包含的表达式，表达式会返回一个字符串。多个字符串用空格间隔。``foreach``会返回这样一个字符串列表。
* ``$(if <condition>,<then-part>)``if语句
* ``$(call <expression>,<parm1>,<parm2>,<parm3>)``调用表达式，表达式中的``$(1),$(2),$(3)``会替换为三个参数。
* ``$(origin <variable>)`` 会告诉我们这个变量是哪里来的。
* ``$(error <text>)``产生一个致命错误，``text``是错误信息，退出
* ``$(waring <text>)``输出一个警告信息，而``make``继续执行
* ``$(shell <command>)``使用``shell``执行``command``命令

