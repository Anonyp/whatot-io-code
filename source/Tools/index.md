title: tools
date: 2015-02-02 19:00:10
categories:
---

此处存放一些工具信息
<!--more-->


### 开发工具

* VIM
* Git（源码控制）
* Eclipse（Java/Python）
* Texmaker（Latex）
* IPython

### 命令行工具与解决方案

**主要来源** [http://youngsterxyf.github.io/tools](http://youngsterxyf.github.io/tools)

* （置顶）[命令行乐园](http://www.commandlinefu.com/commands/browse)

* 使用一行python命令查看/解压缩/创建zip文件：
	1. 查看 - python -m zipfile -l test.zip
	2. 解压缩 - python -m zipfile -e test.zip test
	3. 创建 - python -m zipfile -c release.zip *.py

* 查看所有用户的crontab任务(root权限执行)：
``for user in $(cut -f1 -d: * /etc/passwd); do echo "### Crontabs for $user ###"; crontab -u $user -l; done``

* 删除当前目录下大小为0的文件，且不进一步递归查找：
``find -size 0 -prune -exec rm {} \;``

* 从命令行使用HTTP协议做测试的强大工具：curl，参考
[9 uses for cURL worth knowing](https://httpkit.com/resources/HTTP-from-the-Command-Line/)

* 追踪命令执行过程中的系统调用和信号： ``strace``

* CPU/磁盘/网络等系统资源统计工具： ``dstat``（很好很强大，可替代vmstat，iostat，ifstat）

* 查看PHP会动态加载的扩展模块： ``php -m``

* 递归查找当前目录下所有名为test.txt的文件： ``find ./ -name test.txt``

* Windows上查看端口占用情况： ``netstat -ano``

* 查看LINUX发行版的名称及其版本号的命令：
	1. cat /etc/issue；
	2. lsb_release -a；
	3. cat /etc/redhat-release(针对redhat，Fedora)

* [ack-grep](http://betterthangrep.com/) — 比grep更好用的搜索工具，专为程序员优化

* 禁用触摸板： ``sudo rmmod psmouse``，
开启触摸板：``sudo modprobe psmouse``

* 将man手册转换为pdf格式打印出来阅读，如直接将man命令的手册页转换为pdf格式:

``man -t man | ps2pdf - > man.pdf``

* 显示进程树：``pstree``

* 更新Python第三方软件包：``pip install -U [package_name]``

* 从命令行安装.deb文件：``sudo dpkg -i package.deb``

* 打印环境变量：``printenv``

* 查看通过USB接口连接的硬件：``lsusb``

* 查看已安装的软件包：``dpkg --get-selections``

* 由大到小输出home目录下的所有文件（递归的）：``du -a ~/ | sort -n -r | less``

* 每隔x秒执行命令command，显示输出：``watch -n [number_of_seconds] [command]``

* 监听来自端口的网络输入，存入文件中：
	``netcat -l [recieving_port] > file_copied``

* 将命令的输出通过管道经网络传递给目标ip端口：

``[command] | netcat -w [number_of_seconds_before_timeout] [target_ip] [target_port]``

* 使用tar压缩文件并将文件作为流输出，通过管道经网络传递给目标ip端口：

``sudo tar -czf - [filename] | netcat -w [number_of_seconds_before_timeout] [target_ip] [target_port]``

* ``traceroute``：查看到目标机器/ip的网络路由，如
	``traceroute www.google.com.hk``

* ``nmap``：扫描机器检查开放的网络端口，如
``nmap 127.0.0.1`` — 扫描本地机器的开放端口

* ``tee``：在标准输出显示的同时输出到文件，如``ls | tee ls.txt``

* 显示软件包的详细描述信息：
	1. ``apt-cache show [package_name]``
	2. ``pacman -Q [package_name]``
	3. others

* ls
按文件名逆序输出结果：ls -r；
递归遍历目录：ls -R；
按时间顺序：ls -t，按时间逆序：ls -tr；
按文件大小排序：ls -S。
	（默认按文件名排序，-r表示逆序，-t表示按时间排序，-S表示按文件大小排序，-h表示以方便人阅读的形式输出）

* 系统负载监控：``top/htop/nmon``

* 查看所有进程：``ps -e``或``ps aux``，
另外可通过``ps -e | grep name``来模糊查找是否存在特定进程

* 根据进程名终止进程：``sudo pkill process_name``

* 以树状分支罗列目录内容：``tree [dir_name]``

* 大数据传输：
	1. ``scp -P remoteport username@remoteip:remotedir localdir``    
	或``scp -P remoteport localfile username@remoteip:remotedir``；
	2. scp之外可以使用命令组合：
	``gzip -c /home/xiayf/data | ssh username@ip "gunzip -c - /home/whatot/data" ``

* ``ping ip -f``：持续不断地ping某台服务器(泛洪？)，可能会ping死那台服务器

* 重装Ubuntu，一个个安装程序太麻烦，可先在原来的Ubuntu上执行

``dpkg --get-selections|awk '{print $1}' > o.txt``

导出一个所有已安装程序的列表，然后就可以一键安装了：

``cat o.txt | xargs sudo apt-get install``

* 打包后，以 gzip 压缩：``tar -zcvf /tmp/etc.tar.gz /etc``；

打包后，以 bzip2 压缩：``tar -jcvf /tmp/etc.tar.bz2 /etc``

* 在Linux系统下, 可以用一个命令很容易批量删除.svn的文件夹：
	``find . -name .svn -type d -exec rm -fr {} \;``

* 查看当前正在监听的网络端口：``lsof -i`` 或者 ``netstat -tlnp``

* linux下查看某目录占用的空间大小：``du -h -s``或``du -h -s /* | sort``

* python内嵌的简单便捷HTTP Server：``python -m SimpleHTTPServer Port``

* Python命令行美化输出json数据：``python -mjson.tool json_filename``

或者通过管道``some_cmd | python -mjson.tool``，
也可以直接在命令行使用双引号包围一个json数据字符串来替代json_filename的位置。

而且，如果你安装了 ``Pygments`` 模块，可以高亮地打印JSON：
``echo '{"json":"obj"}' | python -mjson.tool | pygmentize -l json``。

* 保存某个virtualenv中已安装的package列表，并在另一个virtualenv中原样恢复：
	1. ``(some_env)$pip freeze &gt; requirements.txt``;
	2. ``(another_env)$pip install -r requirements.txt``

* [Linux性能分析工具](http://youngsterxyf.github.io/assets/pics/linux-performance-analysis-tools.png)

* [酷毙的Linux单行命令](http://www.commandlinefu.com/commands/browse/sort-by-votes)

* [Gnome/KDE键盘快捷键](http://www.novell.com/coolsolutions/tip/2289.html)


### 日常使用工具

* 桌面环境：xfce 4.10

* Shell：Bash + powerline-shell

* 输入法：Fcitx + sogou拼音

* 翻墙：Goagent + Chrome插件SwitchySharp或Firefox插件AutoProxy

* 命令行窗口复用：tmux + tmux-powerline

* 图形化Web浏览器：Google Chrome/Firefox

* 命令行Web浏览器：lynx

* 终端：Terminator

* 截屏：scrot, xfce4-screenshooter

* 虚拟机：VirtualBox

* 视频播放器：VLC/MPlayer/SMPlayer

* 翻译：Google Translate/有道词典chrome插件/goldendict

* 图片浏览：viewnior,ristretto

* 数据统计图命令行绘制工具：GnuPlot

* PDF阅读器：mupdf/zathura

* BT下载器：Transmission

* RSS阅读：feedly

* 即时通讯：Pidgin

* 办公套件：WPS office LibreOffice

* 数据备份同步：Dropbox + yunio
