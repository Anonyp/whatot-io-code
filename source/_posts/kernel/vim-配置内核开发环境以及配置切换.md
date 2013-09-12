title: vim 配置内核开发环境以及配置切换
date: 2013-06-06 11:12:52
categories: kernel
tags: [vim, kernel]
---

参考文章: [使用vim+ctags+cscope阅读内核源码](http://edsionte.com/techblog/archives/1689)

linux内核开发与linux系统开发有所不同，系统开发是利用系统库，而内核是自包含的，不需要外部系统库的支持，所以与系统开发有所不同。
<!--more-->

## 1. vim 基础配置

我的[vim配置文件](https://github.com/whatot/whatot-dot/)

* master	about all
* ycm 分支[已合并]	一般用于python 开发以及日常使用
* c 分支[已去除]	用于一般的 C 语言开发
* kernel分支[已合并]	专用于内核开发

主要插件 :BundleList

	" My Bundles
	Bundle 'gmarik/vundle'
	Bundle 'STL-improved'
	Bundle 'majutsushi/tagbar'
	Bundle 'tag_in_new_tab'
	Bundle 'autoload_cscope.vim'
	Bundle 'CmdlineComplete'
	Bundle 'Shougo/neocomplete.vim'
	Bundle 'Shougo/neosnippet.vim'
	Bundle 'scrooloose/syntastic'
	Bundle 'plasticboy/vim-markdown'
	Bundle 'bash-support.vim'
	Bundle 'bufexplorer.zip'
	Bundle 'grep.vim'
	Bundle 'a.vim'
	Bundle 'kien/ctrlp.vim'
	Bundle 'L9'
	Bundle 'FuzzyFinder'
	Bundle 'The-NERD-Commenter'
	Bundle 'scrooloose/nerdtree'
	Bundle 'LargeFile'
	Bundle 'tpope/vim-surround'
	Bundle 'kana/vim-smartinput'
	Bundle 'Lokaltog/vim-easymotion'
	Bundle 'matchit.zip'
	Bundle 'asins/vimcdoc'
	Bundle 'vimcn/NERD_commenter.cnx'
	Bundle 'vimcn/tagbar.cnx'
	Bundle 'vimcn/matchit.vim.cnx'

需要安装ctags, cscope

	cd 内核源码目录
	make tags
	make cscope
	set tags+=[tags所在目录]tags
	cs add [cscope.out所在目录]cscope.out

按自己需要将后两条加入vimrc

## 2. session

可以使用session保存配置，快速在内核开发与系统开发配置间切换

可以在 **.vimrc** 中加入这段，使用快捷键

	" session
	" :mksession
	" :source session-file

	set sessionoptions=blank,buffers,sesdir,folds,help,options,tabpages,winsize,resize
	nmap <F3> :mksession! ~/.vim/sessions/
	nmap <C-F3> :so ~/.vim/sessions/

## 3. git branch

可以使用 git branch 切换配置

新建kernel分支

	git branch kernel

如果使用的是 ``~/linux-3.9.4/`` 为内核源码目录，在``make tags cscope``后

在 vimrc 加入:

	set tags+=~/linux-3.9.4/tags
	cs add ~/linux-3.9.4/cscope.out

以及去除其他 tags 与 cscope.out

## 4. ctags, cscope 使用

完成以上步骤以后就可以通过vim进行源码的快速定位了。上述安装的ctags和cscope都属于源码索引工具，因此仅安装两者之一也是可以的。不错，虽然ctags可以快速定位所输入的标签，但cscope的使用方法则更灵活。所以两者同时安装使用起来更方便。下面是一些简单的使用方法，仅供参考。

### 4.1. 使用ctags

ctags的使用特别简单，首先进入源码目录下，打开vim编辑器，在命令模式下输入：tag tag_name 按回车即可。通常tag_name可以是结构体名称，函数名称，宏变量名称等。通常输入上述命令后，因为找到的标签并不唯一，所以还需要配合使用下述命令：

	tfirst：跳至第一个
	tnext：跳至下一个
	tlast：跳至最后一个

### 4.2. 使用cscope

虽然ctags使用方便快捷，但有时候使用cscope更灵活，首先可以看cscope的帮助文件：

cscope 命令:

	add  ：添加一个新的数据库             (用法： add file|dir [pre-path] [flags])
	find ：查询一个模式                   (用法： find c|d|e|f|g|i|s|t name)
	c：找到调用这个函数的函数
	d：找到被这个函数调用的函数
	e：找到这个 egrep 模式
	f：找到此文件夹
	g：找到这个定义
	i：找文件 #包括这个文件
	s：找到这个 C 符号
	t：找到对其的赋值
	help ：显示此信息                     (用法： help)
	kill ：结束一个连接                   (用法： kill #)
	reset：重置所有连接                   (用法： reset)
	show ：显示连接                       (用法： show)

通过上面的帮助文件可以发现如果想找到request_irq函数的定义处代码，即可使用这个命令：
``cs find g request_irq``来进行查找。大多数情况下查找的结果并不唯一，因为需要在多个结果中通过头文件来继续查看。

通常是先通过cscope大致定位到头文件，再通过ctags在该头文件中详细定位。其实这两种工具并不局限于上述用法，更多用法可以再深入使用的过程中慢慢摸索，而且配合正则表达式等效果会更好。
