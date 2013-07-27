title: 转:高效操作Bash
date: 2013-07-27 10:28:07
categories:
tags: [linux, bash]
---

作者：ahei

原文地址：[http://ahei.info/bash.htm](http://ahei.info/bash.htm)


我们在平常工作中大量使用linux, 而使用linux的过程中操作Bash更是非常之频繁, 所以怎样高效的操作Bash是一个非常重要的问题. 下面我结合自己的经验总结一下高效操作Bash的一些技巧.

``````
1 快捷键
	1.1 注意
	1.2 重度推荐
	1.3 常用快捷键
	1.4 高级快捷键
	1.5 总结
2 历史扩展
	2.1 概念
	2.2 事件指示器(Event Designators)
	2.3 单词指示器(Word Designators)
	2.4 修饰符(Modifiers)
	2.5 例子
	2.6 总结
3 shell技巧
	3.1 Here Documents
	3.2 Here Strings
	3.3 进程替换(Process Substitution)
``````
<!--more-->



# 1 快捷键

## 1.1 注意

本文的快捷键表示中, C 表示Ctrl键, M表示Alt健. 这些快捷键中, 有一个小规律,
	对字符操作一般是C开头, 对单词操作一般是M开头. 如果你用SecureCRT,
	默认的话, 会输入不了Alt开头的快捷键, 因为Alt被当作菜单快捷键了, 可以点
	选项 ``->`` 回话选项, 选择tab 终端 ``->`` 仿真 ``->`` Emacs,
	把”使用Alt键作为元键”打勾. 如果你用gnome-terminal,
	默认状态下也输入不了Alt开头的快捷键，也被当作菜单快捷键了，可以点 编辑
	``->`` 键盘快捷键, 把＂启用菜单快捷键＂前面的勾去掉.

下面的快捷键中很多以Ctrl键开头, 很多键盘的Ctrl键并不是很好按, 可以尝试把Ctrl键和Capslock键交换.

## 1.2 重度推荐

**C-r**

有时候，如果你想重新输入以前输入过的某条命令怎么办? 我见过两种做法：

1. 不停的按向上方向键，试图找出那条命令
2. 输入history命令，然后找到那条命令，或者grep一把history命令的输出

其实, 你有更好的选择, 那就是按 C-r, 然后输入你想要的命令中含有的单词, 就会出现含有这个单词的命令, 如果它不是你想要的命令, 就继续按C-r, 知道出现你想要的命令为止. C-r效果:

	(reverse-i-search)`ls': ls a b c

**M-.**

我经常见别人用``mkdir long-long-long-name-dir``后, 再输入``cd``,
	后面跟那个长的不能再长的目录名, 这时候我就会告诉他,
	其实你输入完``cd``后, 可以按``M-.``,
	就可以自动输入那个长的不能再长的目录名了. 其实,
	``M-``.的真正作用就是把上一条命令的最后一个参数输入到当前命令行.
	非常非常之方便, 强烈推荐. 如果继续按``M-.``,
	会把上上条命令的最后一个参数拿过来. 同样,
	如果你想把上一条命令第一个参数拿过来咋办呢? 用``M-0 M-.``,
	就是先输入``M-0``, 再输入``M-.``. 如果是上上条命令的第一个参数呢?
	当然是``M-0 M-. M-.``了.

## 1.3 常用快捷键

**程序控制**

<table>
	<caption>程序控制</caption>
	<thead>
		<tr>
			<th>意义</th>
			<th>快捷键</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>终止当前在前台运行的程序</td>
			<td>C-c</td>
		</tr>
		<tr>
			<td>挂起当前在前台运行的程序</td>
			<td>C-z</td>
		</tr>
		<tr>
			<td>如果光标在行首且当前行没有输入任何字符, C-d会退出当前会话</td>
			<td>C-d</td>
		</tr>
	</tbody>
</table>


**光标移动**

<table>
	<caption>光标移动</caption>
	<thead>
		<tr>
			<th>意义</th>
			<th>快捷键</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>向前(Forward)移动一个字符</td>
			<td>C-f</td>
		</tr>
		<tr>
			<td>向后(Backward)移动一个字符</td>
			<td>C-b</td>
		</tr>
		<tr>
			<td>向前移动一个单词</td>
			<td>M-f</td>
		</tr>
		<tr>
			<td>向后移动一个单词</td>
			<td>M-b</td>
		</tr>
		<tr>
			<td>移动光标到行首</td>
			<td>C-a</td>
		</tr>
		<tr>
			<td>移动光标到行尾</td>
			<td>C-e</td>
		</tr>
	</tbody>
</table>

**编辑**

<table>
	<caption>编辑</caption>
	<thead>
		<tr>
			<th>意义</th>
			<th>快捷键</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>向前删一个字符</td>
			<td>C-d</td>
		</tr>
		<tr>
			<td>向后删一个字符</td>
			<td>C-h</td>
		</tr>
		<tr>
			<td>向前删一个单词</td>
			<td>M-d</td>
		</tr>
		<tr>
			<td>向后删一个单词, 单词之间以符号分割</td>
			<td>C-M-h</td>
		</tr>
		<tr>
			<td>向后删一个单词, 单词之间以空格分割</td>
			<td>C-w</td>
		</tr>
		<tr>
			<td>清屏, 相当于命令clear, 有了这个快捷键, 就不用每次努力的敲clear了</td>
			<td>C-l</td>
		</tr>
		<tr>
			<td>删除当前光标到行尾的字符</td>
			<td>C-k</td>
		</tr>
		<tr>
			<td>删除当前光标到行首的字符</td>
			<td>C-u</td>
		</tr>
		<tr>
			<td>粘贴删除环里面的第一项</td>
			<td>C-y</td>
		</tr>
		<tr>
			<td>粘贴删除环里面的后面的项</td>
			<td>M-y</td>
		</tr>
		<tr>
			<td>undo</td>
			<td>C-/</td>
		</tr>
		<tr>
			<td>取出上一条命令的最后一个参数</td>
			<td>M-.</td>
		</tr>
	</tbody>
</table>


对于``C-M-h``和``C-w``的区别, 看下面这个例子:

如果当前光标前面的字符串为"abc def-ghi", ``C-M-h``会删掉ghi,
	但是``C-w``会删掉”def-ghi”, 也就是说,
	``C-M-h``向后删的时候碰到非字母和数字就会停止, 但是``C-w``碰到空格才会停止.

Bash下有一个删除环(``kill-ring``),
	所有被删除的东西(用C-d删除的字符不算)都会进入这个环,
	``C-y``会粘贴环里面最近进去的项, 想要粘贴后面的项, 必须在按``C-y``后,
	不停的按``M-y``, 直到出来你想要的项为止.

有时候, 你想搜索某个文件中是否有TAB键, 你这时候会怎么做呢? 你或许会用grep, 在你输入完grep后, 你再按TAB, 这时候会出来什么? 什么都没出现! 再按? 出来:

Display all N possibilities? (y or n)

这是为何呢? 因为TAB是补全键. 那么是否是输入不了TAB吗? 不是! 按``C-v``后, 再按TAB即可. 同样, 想输入C-a, C-b也是同样的道理.

**历史命令操作**

<table>
	<caption>历史命令操作</caption>
	<thead>
		<tr>
			<th>意义</th>
			<th>快捷键</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>从历史命令列表中取下一条命令, 相当于向下方向键</td>
			<td>C-n</td>
		</tr>
		<tr>
			<td>从历史命令列表中取上一条命令, 相当于向上方向键</td>
			<td>C-p</td>
		</tr>
		<tr>
			<td>向后增量搜索历史命令, 非常方便, 严重推荐, 有了它, 以前输入过的很长的命令, 可以不用重复输入</td>
			<td>C-r</td>
		</tr>
		<tr>
			<td>循环执行历史命令</td>
			<td>C-o</td>
		</tr>
	</tbody>
</table>


用``C-p``取出历史命令列表中某一个命令后,
	按``C-o``可以在这条命令到历史命令列表后面的命令之间循环执行命令,
	比如历史命令列表中有50条命令, 后面三项分别是命令A, 命令B, 命令C,
	用``C-p``取出命令A后, 再按``C-o``就可以不停的在命令A, 命令B,
	命令C中循环执行这三个命令. ``C-o``有一个非常好用的地方,
	比如用cp命令在拷贝一个大目录的时候, 你肯定很想知道当前的拷贝进度,
	那么你现在该怎样做呢? 估计很多人会想到不停的输入``du -sh dir``去执行,
	但用``C-o``可以非常完美的解决这个问题, 方法就是:

* 输入``du -sh dir``, 按回车执行命令
* ``C-p, C-o``, 然后就可以不停的按``C-o``了, 会不停的执行``du -sh dir``这条命令

其实上面这个问题也可以用watch命令解决:

``watch -n 1 -d du -sh dir``

## 1.4 高级快捷键

<table>
	<caption>高级快捷键</caption>
	<thead>
		<tr>
			<th>意义</th>
			<th>快捷键</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>从当前光标处向前搜索字符</td>
			<td>C-]</td>
		</tr>
		<tr>
			<td>从当前光标处向后搜索字符</td>
			<td>C-M-]</td>
		</tr>
		<tr>
			<td>交换当前光标下的字符和光标前面的一个字符, 交换后, 光标向后移东一个字符</td>
			<td>C-t</td>
		</tr>
		<tr>
			<td>交换当前光标所在单词和光标前面一个单词, 交换后, 光标向后移动一个单词</td>
			<td>M-t</td>
		</tr>
		<tr>
			<td>把单词首字符变成大写, 其他变成小写</td>
			<td>M-c</td>
		</tr>
		<tr>
			<td>把单词变成小写</td>
			<td>M-l</td>
		</tr>
		<tr>
			<td>把单词变成大写</td>
			<td>M-u</td>
		</tr>
		<tr>
			<td>删除当前光标前面所有的空白字符</td>
			<td>M-\</td>
		</tr>
		<tr>
			<td>向后非增量搜索历史命令</td>
			<td>M-p</td>
		</tr>
		<tr>
			<td>相当于TAB健</td>
			<td>C-i</td>
		</tr>
		<tr>
			<td>相当于回车键</td>
			<td>C-m/C-j</td>
		</tr>
		<tr>
			<td>在当前光标处和上一次光标处不停的移动</td>
			<td>C-x C-x</td>
		</tr>
	</tbody>
</table>

## 1.5 总结

其实, 上面所说的快捷键并不是由Bash来控制的, 而是有一个叫readline的库来控制的, readline库用在很多地方, 比如gdb, mysql, 你使用gdb的时候, 是不是很奇怪, 为啥它也能用上下方向键取出前面后面的命令? 因为它用的也是readline库. 所以只要掌握了readline, 就掌握了Bash, gdb, mysql等程序里面的快捷键操作技巧. readline是一个非常非常强悍的库, 它有两种模式, 一个是Emacs模式, 另外一个是vi模式, Emacs模式非常适合在命令行下使用, 我上面说的快捷键都是针对Emacs模式来说的. readline的Emacs模式下的光标移动, 编辑等快捷键和Emacs下的快捷键也非常相近. 所以你学会了这些快捷键, 也快入门Emacs了, :) . readline也可以自定义快捷键, 它还有一套配置语法. 关于它的详细介绍, 可以man readline或者info readline, 也可以看看大牛王垠写的readline介绍.

# 2 历史扩展

## 2.1 概念

首先举个例子:
首先输入一条命令:

	ls abc def ghi

再输入:

	!!*:s/b/d

那么实际上执行的命令是:

	adc def ghi

我来解释一下, ``!!``表示从命令历史列表中取上一条历史命令”ls abc def ghi”,
	``*``表示选择取刚才选择的命令的所有参数, 即: “abc def ghi”,
	``:s/b/d``表示对刚才取出来的参数”abc def ghi”进行替换, 把第一个出现的b替换成d

从上面可以看出, 操作历史命令分为三步:

1. 首先从历史命令列表中选择某条命令, 被选择到的命令被称作 事件(event) (对应上面的!!)
2. 再从选择好的事件中选择一部分单词(words), 事件中的每个单词以空格分割(对应上面的*)
3. 最后对选择好的一部分单词进行修改(Modifiers)

## 2.2 事件指示器(Event Designators)

事件指示器用来从历史命令列表中选择一条命令, 也就是选择事件

* !n
选择历史命令列表中第n条命令

* !-n
选择倒数第n条命令

* !!
选择上一条命令, 相当于!-1, 和 C-p 的作用也一样

* !string
选择最近的以string开头的命令

* !?string[?]
选择最近的包含string的命令, 如果该指示器后面是换行符, 则可以不用输入结尾的”?”

* ^string1^string2
取上一条命令, 并把第一个出现的string1替换成string2

* !#
引用目前输入的所有命令, 比如输入:``more a !#``
, 那么最终执行的命令就是:``more a more a``

## 2.3 单词指示器(Word Designators)

单词指示器用来从被选择好的事件中选择一部分单词, 单词指示器必须以冒号(:)和事件指示器分割开来, 除非单词指示器以^, $, *, -, %开头

* 0
选择第0个word, 也就是命令. 假如事件为”ls abc”, 那么单词指示器0选择的word即为”ls”

* n
选择第n个word

* ^
选择命令的第一个参数, 也就是第一个word, 相当于单词指示器1

* $
选择命令的最后一个参数

* %
选择最近的与 “?string?” 搜索相匹配的单词

* x-y
选择第x到第y个word, -y表示0-y

* *
选择命令的所有参数, 相当于1-$

* x*
x-$的缩写

* x-
类似x*, 不过不包含最后一个word. -选择除最后一个word外所有的words

## 2.4 修饰符(Modifiers)

对选择的单词进行修改, 修饰符可以出现多次, 每个修饰符要以冒号开头

* p
打印新命令, 但不执行

* s/old/new/
把 第一次出现的 old替换成new, 如果分隔符”/”是最后一个字符的话, 可以省略.
就像sed中一样, 分隔符”/”可以用其他字符代替, 比如s:old:new:. new中出现的``&``将被old代替. 如果old省略, 那么就用上一次替换用的old代替.

* ``&``
重复上一次替换

* g
使修饰符所做的修改应用于整个选择的单词. 类似于sed中的s命令最后的g, 可配合:s和:&修饰符使用, 比如:gs/old/new则对整个事件进行替换.

* a
和g作用一样

* G
使后面的:s修饰符对每个word只替换一次

## 2.5 例子

## 例一

从别的机器的一个目录拷贝一个a.log文件, 执行:

	scp user@machine:/home/user/a/a.log .

后来执行:

	ls a.log
	rm -rf a.log

这时候再想拷贝一下b/b.log, 这时候就可以这样做:

	!scp:gs/a/b

如果只想看看用历史扩展出来的命令, 那可以这样:

	!scp:gs/a/b/:p

## 例二

从别的机器同时拷贝a/a.log和b/b.log:

	scp user@mbchine:/home/user/a/a.log . && !#-:gs/a/b

上面的!#为事件指示器, 选择前面已经输入的命令
``scp user@mbchine:/home/user/a/a.log . &&``, ``-``为单词指示器,
选择除最后一个word, 即``&&``外的所有words, 也就是
``scp user@mbchine:/home/user/a/a.log . ``, 最后的``:gs/a/b``为修饰符, 对刚才选择的words进行全局替换, 把a替换成b, 最后就成了
``scp user@mbchine:/home/user/b/b.log .``, 那么最终命令也就成了
``scp user@mbchine:/home/user/a/a.log . && scp user@mbchine:/home/user/b/b.log
.``

## 2.6 总结

上面的例子都可以用前面所说的快捷键完成, 不过灵活利用历史扩展有时候还是能更高效的完成同样的事情

# 3 shell技巧

## 3.1 Here Documents

	<<[-]word
	here-documents
	delimiter

把here-documents作为某个命令的标准输入, 例子:

	grep a << EOF
	asdf
	qweszd
	asdf
	EOF

## 3.2 Here Strings

	<<< here-strings

把word作为命令的标准输入, 例子:

	grep a <<< abc

## 3.3 进程替换(Process Substitution)

假如我现在想比较两个目录dir1和dir2中的文件有啥不同, 我想很多人会这样做:

	ls dir1 > 1
	ls dir2 > 2
	diff 1 2

但你试试这样:

	diff <(ls dir1) <(ls dir2)

是不是也可以? 很神奇吧. 上面的这个语法``<(command)``就是进程替换.
``<(command)``表示把command的输出生成一个临时文件,
并把这个文件名作为另外一个命令的参数. 对于上面的命令, 就是把
``ls dir1``命令的输出生成一个临时文件, 并把临时文件名做为diff命令的第一个参数. 再举一个例子:

	wget -q -O >(cat) http://baidu.com

wget命令会把下载后的文件保存到文件中去,
	但是我们可以用上面的命令不让它保存到文件中去, 而是显示出来.
	wget的``-O``选项后本来应该是一个文件名的参数, 但是我们现在用``>(cat)``代替, 表示wget下载下来的内容放到一个临时文件中, 然后把这个临时文件名再传给>()里面的cat命令.

灵活运用进程替换, 将会非常的方便, 严重推荐
