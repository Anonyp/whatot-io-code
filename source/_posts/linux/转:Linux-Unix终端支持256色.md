title: 转:Linux/Unix终端支持256色
date: 2013-06-30 18:11:24
categories:
tags: [shell, linux]
---

[原文来源:](http://yysfire.github.io/linux/%E5%A6%82%E4%BD%95%E6%9F%A5%E7%9C%8BLinux%E7%BB%88%E7%AB%AF%E6%98%AF%E5%90%A6%E6%94%AF%E6%8C%81256%E8%89%B2.html)

运行以下命令, 若256种颜色全部输出，就表示支持256色。

	$ (x=`tput op` y=`printf %76s`;for i in {0..256};do o=00$i;echo -e ${o:${#o}-3:3} `tput setaf $i;tput setab $i`${y// /=}$x;done)

或者运行``tput colors``，输出现在终端所支持的颜色位数

下列命令会输出你所支持的所有终端模拟器，以及它们支持的颜色位数，可能的值有：8, 15, 16, 52, 64, 88 和 256.

	$ for T in `find /usr/share/terminfo -type f -printf '%f '`;do echo "$T `tput -T $T colors`";done|sort -nk2|tail -n20

可以在终端的配置文件，例如 .bashrc 中加入以下内容，使其支持256色：

	export TERM=vte-256color

VIM 终端支持 256 色，需要在vimrc中添加

	set t_Co=256
