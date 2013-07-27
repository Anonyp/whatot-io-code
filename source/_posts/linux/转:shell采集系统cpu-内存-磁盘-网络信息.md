title: 转:shell采集系统cpu 内存 磁盘 网络信息
date: 2013-07-27 16:29:14
categories:
tags:
---

原文地址：
[http://www.centos.bz/2013/07/shell-collect-system-cpu-memory-disk-network-info/](http://www.centos.bz/2013/07/shell-collect-system-cpu-memory-disk-network-info/)

```
cpu信息采集
	cpu使用率
	cpu负载
内存采集
	应用程序使用内存
	MEM使用量
	SWAP使用大小
磁盘信息采集
	disk io
网络
	流量
	包量
```
<!--more-->

# cpu信息采集

## cpu使用率

采集算法

通过/proc/stat文件采集并计算CPU总使用率或者单个核使用率。以cpu0为例，算法如下：

1. cat /proc/stat | grep ‘cpu0’得到cpu0的信息
2. cpuTotal1=user+nice+system+idle+iowait+irq+softirq
3. cpuUsed1=user+nice+system+irq+softirq
4. sleep 30秒
5. 再次cat /proc/stat | grep 'cpu0' 得到cpu的信息
6. cpuTotal2=user+nice+system+idle+iowait+irq+softirq
7. cpuUsed2=user+nice+system+irq+softirq
8. 得到cpu0 在30秒内的单核利用率：
(cpuUsed2 – cpuUsed1) * 100 / (cpuTotal2 – cpuTotal1)

相当于使用``top –d 30``命令，把user、nice、system、irq、softirq五项的使用率相加。

shell代码：

	a=$(`cat /proc/stat | grep -E "cpub" | awk -v total=0 '{$1="";for(i=2;i<=NF;i++){total+=$i};used=$2+$3+$4+$7+$8 }END{print total,used}'`)
	sleep 30
	b=$(`cat /proc/stat | grep -E "cpub" | awk -v total=0 '{$1="";for(i=2;i<=NF;i++){total+=$i};used=$2+$3+$4+$7+$8 }END{print total,used}'`)
	cpu_usage=$(((${b[1]}-${a[1]})*100/(${b[0]}-${a[0]})))

## cpu负载

采集算法：

读取/proc/loadavg得到机器的1/5/15分钟平均负载，再乘以100。

shell代码：

	cpuload=$(`cat /proc/loadavg | awk '{print $1,$2,$3}'`)
	load1=${cpuload[0]}
	load5=${cpuload[1]}
	load15=${cpuload[2]}



# 内存采集

## 应用程序使用内存

采集算法：

读取/proc/meminfo文件，(MemTotal – MemFree – Buffers – Cached)/1024得到应用程序使用内存数。

shell代码：

	awk '/MemTotal/{total=$2}/MemFree/{free=$2}/Buffers/{buffers=$2}/^Cached/{cached=$2}END{print (total-free-buffers-cached)/1024}'  /proc/meminfo

## MEM使用量

采集算法：

读取/proc/meminfo文件，MemTotal – MemFree得到MEM使用量。

shell代码：

	awk '/MemTotal/{total=$2}/MemFree/{free=$2}END{print (total-free)/1024}'  /proc/meminfo

## SWAP使用大小

采集算法：

通过/proc/meminfo文件，SwapTotal – SwapFree得到SWAP使用大小。

shell代码：

	awk '/SwapTotal/{total=$2}/SwapFree/{free=$2}END{print (total-free)/1024}'  /proc/meminfo



# 磁盘信息采集

## disk io

1. IN：平均每秒把数据从硬盘读到物理内存的数据量

采集算法：

读取/proc/vmstat文件得出最近240秒内pgpgin的增量，把pgpgin的增量再除以240得到每秒的平均增量。
相当于vmstat 240命令bi一列的输出。

shell代码：

	a=`awk '/pgpgin/{print $2}' /proc/vmstat`
	sleep 240
	b=`awk '/pgpgin/{print $2}' /proc/vmstat`
	ioin=$(((b-a)/240))

2. OUT：平均每秒把数据从物理内存写到硬盘的数据量

采集算法：

读取/proc/vmstat文件得出最近240秒内pgpgout的增量，把pgpgout的增量再除以240得到每秒的平均增量。
相当于vmstat 240命令bo一列的输出。

shell代码：

	a=`awk '/pgpgout/{print $2}' /proc/vmstat`
	sleep 240
	b=`awk '/pgpgout/{print $2}' /proc/vmstat`
	ioout=$(((b-a)/240))

# 网络

## 流量

以http://www.centos.bz/为例，eth0是内网，eth1外网，获取60秒的流量。
机器网卡的平均每秒流量

采集算法：

读取/proc/net/dev文件，得到60秒内发送和接收的字节数（KB），然后乘以8，再除以60，得到每秒的平均流量。
shell代码：

	traffic_be=$(`awk 'BEGIN{ORS=" "}/eth0/{print $2,$10}/eth1/{print $2,$10}' /proc/net/dev`)
	sleep 60
	traffic_af=$(`awk 'BEGIN{ORS=" "}/eth0/{print $2,$10}/eth1/{print $2,$10}' /proc/net/dev`)
	eth0_in=$(( (${traffic_af[0]}-${traffic_be[0]})/60 ))
	eth0_out=$(( (${traffic_af[1]} ${traffic_be[1]})/60 ))
	eth1_in=$(( (${traffic_af[2]} ${traffic_be[2]})/60 ))
	eth1_out=$(( (${traffic_af[3]} ${traffic_be[3]})/60 ))

## 包量

机器网卡的平均每秒包量

采集算法：

读取/proc/net/dev文件，得到60秒内发送和接收的包量，然后除以60，得到每秒的平均包量。

shell代码：

	packet_be=$(`awk 'BEGIN{ORS=" "}/eth0/{print $3,$11}/eth1/{print $3,$11}' /proc/net/dev`)
	sleep 60
	packet_af=$(`awk 'BEGIN{ORS=" "}/eth0/{print $3,$11}/eth1/{print $3,$11}' /proc/net/dev`)
	eth0_in=$(( (${packet_af[0]}-${packet_be[0]})/60 ))
	eth0_out=$(( (${packet_af[1]} ${packet_be[1]})/60 ))
	eth1_in=$(( (${packet_af[2]} ${packet_be[2]})/60 ))
	eth1_out=$(( (${packet_af[3]} ${packet_be[3]})/60 ))
