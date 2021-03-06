title: 转:关于c语言结构体偏移的一点思考
date: 2013-06-26 22:38:45
categories: c
tags: [c, language, kernel, program]
---

[原文来源:](http://my.oschina.net/gschen/blog/140463)

## 前言

相信大家在c语言程序开发的过程一定都使用过结构体，那么不知你对结构体中成员变量偏移这块是如何理解的？本文将和大家一起分享下，本人最近关于c语言中结构体偏移的一些思考和总结。

另外这篇博文还可以帮你更好的理解这个问题[c语言中两种宏定义的区别](http://www.oschina.net/question/271937_116085)，关于这个思考有哪些方面的意义，细心的你可能发现本文所属的类别为**linux内核源码分析**，而并非
**GNU C语言编程**，可能有些同学会有些许好奇。不过不用着急，如果对本篇博文意义感兴趣的同学，可继续关注后续的博文，会有进一步的阐述。

<!--more-->

## 示例1

我们先来定义一下需求：

已知结构体类型定义如下：

	struct node_t{
	    char a;
	    int b;
	    int c;
	};

且结构体1Byte对齐

	#pragma pack(1)

求：

结构体``struct node_t``中成员变量``c``的偏移。

>注：这里的偏移量指的是相对于结构体起始位置的偏移量。

看到这个问题的时候，我相信不同的人脑中浮现的解决方法可能会有所差异，下面我们分析以下几种可能的解法：

### 方法1

如果你对c语言的库函数比较熟悉的话，那么你第一个想到的肯定是offsetof函数(其实只是个宏而已，先姑且这样叫着吧)，我们
``man 3 offsetof``查看函数原型如下：

	#include <stddef.h>
	
	      size_t offsetof(type, member);

有了上述的库函数，我们用一行代码就可以搞定：

	offsetof(struct node_t, c);

当然这并非本文探讨的重点，请继续阅读。

------

### 方法2

当我们对c语言的库函数不熟悉的时候，此时也不要着急，我们依然可以使用我们自己的方法来解决问题。

最直接的思路是：结构体成员变量c的地址 - 结构体起始地址

我们先来定义一个结构体变量node：

	struct node_t node;

接着来计算成员变量c的偏移量：

	(unsigned long)(&(node.c)) - (unsigned long)(&node)

``&(node.c)``为结构体成员变量c的地址，并强制转化为``unsigned long``；

``&node``为结构体的起始地址，也强制转化为``unsigned long``；

最后我们将上述两值相减，得到成员变量c的偏移量；

------

### 方法3

按照方法2的思路我们在不借助库函数的情况下，依然可以得到成员变量c的偏移量。但作为程序员，我们应该善于思考，是不是可以针对上面的代码做一些改进，使我们的代码变得更简洁一些？在做具体的改进之前，我们应该分析方法2存在哪些方面的问题。

相信不用我多说，细心的你一定已经察觉到，方法2中最主要的一个问题是我们自定义了一个结构体变量node，虽然题目中并未限制我们可以自定义变量，但当我们遇到比较严且题目中不允许自定义变量的时候，此时我们就要思考新的解决方法。

在探讨新的解决方法之前，我们先来探讨一个有关偏移的小问题：

#### 小问题

这是一道简单的几何问题，假设在座标轴上由A点移动到B点，如何计算B相对于A的偏移？这个问题对于我们来说是非常的简单，可能大部分人都会脱口而出并得到答案为B-A。

那么这个答案是否完全准确呢？比较严谨的你觉得显然不是，原因在于，当A为坐标原点即A=0的时候，上述答案B-A就直接简化为B了。

这个小小的简单的问题，对于我们来说有什么启示呢？

我们结合方法2的思路和上述的小问题，是不是很快就得到了下面的关联:

	(unsigned long)(&(node.c)) - (unsigned long)(&node)

和

	B - A

我们小问题的思路是当A为坐标原点的时候，B-A就简化为B了，那么对应到我们的方法2，当node的内存地址为0即``(&node==0)``的时候，上面的代码可简化为：

	(unsigned long)(&(node.c))

由于node内存地址==0了，所以

	node.c      //结构体node中成员变量c

我们就可以使用另外一种方式来表达了，如下：

	((struct node_t *)0)->c

上述代码应该比较好理解，由于我们知道结构体的内存地址编号为0，所以我们就可以直接通过内存地址的方式来访问该结构体的成员变量，相应的代码的含义就是
获取内存地址编号为0的结构体``struct node_t``的成员变量c。

此时，我们的偏移求法就消除了struct node_t node这个自定义变量，直接一行代码解决,：

	(unsigned long)(&(((struct node_t *)0)->c))

上述的代码相对于方法2是不是更简洁了一些。

这里我们将上面的代码功能定义为一个宏，该宏的作用是用来计算某结构体内成员变量的偏移(后面的示例会使用该宏)：

	#define OFFSET_OF(type, member) (unsigned long)(&(((type *)0)->member))

使用上面的宏，就可以直接得到成员变量c在结构体``struct node_t``中的偏移为：

	OFFSET_OF(struct node_t, c)

## 示例2

和示例1一样，我们先定义需求如下：

已知结构体类型定义如下：

	struct node_t{
	    char a;
	    int b;
	    int c;
	};

``int *p_c``，该指针指向``struct node_t x``的成员变量c

结构体1Byte对齐

	#pragma pack(1)

求：

结构体x的成员变量b的值？

拿到这个问题的时候，我们先做一下简单的分析，题目的意思是根据一个指向某结构体成员变量的指针，如何求该结构体的另外一个成员变量的值。

那么可能的几种解法有：

### 方法1

由于我们知道结构体是1Byte对齐的，所以这道题最简单的解法是：

	*(int *)((unsigned long)p_c - sizeof(int))

上述代码很简单，成员变量c的地址减去``sizeof(int)``从而得到成员变量b的地址，然后再强制转换为
``int *``，最后再取值最终得到成员变量b的值；

### 方法2

方法1的代码虽然简单，但扩展性不够好。我们希望通过p_c直接得到指向该结构体的指针p_node，然后通过p_node访问该结构体的任意成员变量了。

由此我们得到计算结构体起始地址p_node的思路为：

	（成员变量c的地址p_c）减去（c在结构体中的偏移）

由示例1，我们得到结构体``struct node_t``中成员变量c的偏移为：

	(unsigned long)&(((struct node_t *)0)->c)

所以我们得到结构体的起始地址指针 ``p_node`` 为：

	(struct node_t *)((unsigned long)p_c - (unsigned long)(&((struct node_t *)0)->c))

我们也可以直接使用示例1中定义的``OFFSET_OF``宏，则上面的代码变为：

	(struct node_t *)((unsigned long)p_c - OFFSET_OF(struct node_t, c))

最后我们就可以使用下面的代码来获取成员变量a,b的值：

	p_node->a
	 
	p_node->b

我们同样将上述代码的功能定义为如下宏：

	#define STRUCT_ENTRY(ptr, type, member) (type *)((unsigned long)(ptr)-OFFSET_OF(type, member))

该宏的功能是通过结构体任意成员变量的指针来获得指向该结构体的指针。

我们使用上面的宏来修改之前的代码如下：

	STRUCT_ENTRY(p_c, struct node_t, c)

* p_c为指向结构体struct node_t成员变量c的指针；
* struct node_t结构体类型；
* c为p_c指向的成员变量；

## 注：

上述关于地址运算的一些说明：

	int a = 10;
	int * p_a = &a;

设 ``p_a == 0x95734104``；

以下为编译器计算的相关结果：

	p_a + 10 == p_a + sizeof(int)*10 =0x95734104 + 4*10 = 0x95734144

	(unsigned long)p_a + 10 == 0x95734104+10 = 0x95734114

	(char *)p_a + 10 == 0x95734104 + sizeof(char)*10 = 0x95734114

从上述三种情况，相信你应该能体会到我所要表达的意思了。

## 结论

本文通过几个示例描述了c语言结构体有关偏移的一些有意思的事情，希望能够对你有所帮助。为什么会有上述思考，相信有些同学已经看出一些端倪，这也正是后续博文将要描述的主题。

如文中有错误之处，欢迎指出。

>引用

[1] http://isis.poly.edu/kulesh/stuff/src/klist/

[2] https://www.kernel.org/doc/Documentation/CodingStyle

[3] http://www.kroah.com/log/linux/container_of.html
