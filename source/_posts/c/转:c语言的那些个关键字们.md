title: 转:C语言的那些个关键字们
date: 2013-06-05 10:38:45
categories: c
tags: [c, language, program]
---

[[转] C语言的那些个关键字们](http://blogread.cn/it/article/5844)

首先，C语言中到底有多少个关键字呢？木有错，ANSI C规定是32个！ 他们分别是：auto double int struct break else long switch case enum register typedef char extern return union const float short unsigned continue for signed void default goto sizeof volatile do if while static。

<!--more-->

别看那一堆了字母了，直接看下面的分类介绍：

## 第一类：数据类型关键字

这一类别的关键字有：

	char          //声明字符型变量或函数
	double       //声明双精度变量或函数
	enum         //声明枚举类型
	float          //声明浮点型变量或函数
	int            //声明整型变量或函数
	long          //声明长整型变量或函数
	short         //声明短整型变量或函数
	signed       //声明有符号类型变量或函数
	struct        //声明结构体变量或函数
	union         //声明共用体数据类型
	unsigned     //声明无符号类型变量或函数
	void           //声明函数无返回值或无参数，声明无类型指针


这一类别的关键字无需过多的说明，基本都是我们经常用到的，但是，仍然有些是我们需要注意的东西：

1. C 标准并未定义指针、整数型（int）、长型（long）为特定的位数目。在32位体系结构下，一般int和long都是32位长；值得注意的是，64位机器下，很多程序设计环境，“int”变量仍然是 32 位宽，“long”和指针是 64 位宽。注意，这里说的只是一般情况下！详细的解释可以看这里。
2. union声明的联合数据结构，里面的数据是共享内存的，可以看我的另一篇日志。
3. unsigned声明的是一个无符号数据类型，也就是说，如果unsigned int i; 需要注意变量i永远不可能等于复数，除非强制类型转换。


## 第二类：控制语句关键字

	for         //循环语句
	do         //循环语句的循环体
	while      //循环语句的循环条件
	break     //跳出当前循环
	continue //结束当前循环，开始下一次循环
	if           //条件分支语句
	else       //条件分支语句
	goto      //无条件跳转语句
	switch    //不解释
	case      //不解释
	default    //不解释
	return     //返回语句

这些关键字估计都快被用烂了吧？不解释！

下面，开始做点有意义的事情，着重解释以下的关键字：

## 第三类：存储类型关键字

	auto
	extern
	register
	static


让我们一一来看这四个关键字：

1. auto关键字： 声明变量的生存期为自动，即将不在任何类、结构、枚举、联合和函数中定义的变量视为全局变量，而在函数中定义的变量视为局部变量。不明白？无视他好了，编译器默认的缺省情况下，所有的变量都是auto的。

2. extern关键字： 我们都知道，一个变量或函数，可以在a.c文件中定义，而在b.c文件中使用，这个时候，b.c就需要使用extern关键字来声明这个变量和函数，目的是为了告诉编译器，这个函数在b.c之外，别让我编译不过！

3. register关键字： 这个关键字就很少用到了，但是却十分有用。它的目的是告诉编译器尽量把这个变量放到寄存器中，这样提高存取速度，但是不是能真的放到寄存器中却不一定，毕竟寄存器的数量是有限的。在我们的二进制翻译器中，这个关键字被巧妙的用于线程切换。

4. static关键字： 好吧，我承认我土了，我就是栽在这个关键字上的。static有两种修饰，分别如下：

>**(1)修饰变量**：变量分为全局变量和静态变量，都存储在内存的静态区中。

>首先，当static修饰全局变量的时候，该变量的作用域仅被限定在当前文件中，别的文件即使使用extern关键字也无法使用这个变量。

>其次，当static修饰局部变量的时候，该变量在哪个函数体中定义，就只能在哪个函数体中使用。也许你会说，这不跟普通局部变量一样么？不一样！别忘了他是被存储在内存的静态区中，所谓的静态区就是全局区，用来存放全局变量和静态变量的，程序不结束，这个区是不会被释放的，所以即使定义静态局部变量的函数结束，改静态局部变量仍然存在，下次访问改函数的时候，这个变量的值仍然是上次的值！举个例子把：

>		void foo(){
			static int i=0;
			i++;
			printf("%d\n",i);
		}
		int main(){
			foo();
			foo();
		}

>这个小例子的执行结果是什么呢？答案是：

>		1
>		2

>对的，静态局部变量只能被初始化一次，并且值会被保留，使用这个有两个好处，一个是可以计算函数被调用的次数，一个是可以减少函数构建局部变量的开销，自己体会一下把。

>**(2)修饰函数**： 经常见这种形式，但没怎么用过，也就没去想。其实这个作用跟静态全局变量相似，也是限定函数的作用域为本文件。这样作的好处就是不用操心是否会跟别人编写的文件里的函数重名。（我这里栽了一下，太弱了～不甘心阿！）


## 第四类：其他关键字

	const
	sizeof
	typedef
	volatile

下面，也是一样，一一解释下这些关键字：

### 1. const关键字：

这是一个很有意思的关键字，他修饰的变量是只读的，不能被修改；很多时候，编译器会将其优化成一个常量。const经常被用来修饰函数的参数，表示不希望这个参数值被函数体内的代码意外的改变。其实，最有意思的是用const修饰一个指针，让我们看下面这个例子：

	const int *p;   //p可变，p指向的对象不可变
	int const *p;   //同上
	int *const p;   //p不可变，p指向的对象可变
	const int *const p; //p和p指向的对象都不可变

这些各表示什么呢？注释里面给出了答案！是不是很不好记？我们只需要记得，const修饰的是*p的时候，p指向的内容不可变；const修饰的是p的时候，p就不可变！

### 2. sizeof关键字：

很多人也许会大吃一斤，我类个去，sizeof居然是关键字？（高手请无视这里，我当初就是这种表现）。不错，sizeof确实是关键字，而不是库函数！所以，如果编译时得不到一个数组的大小，那么就不能使用sizeof关键字来获取改数组的大小！

### 3. typedef关键字：

typedef说白了就是给一个已知的类型起一个外号。让我们考虑一个问题：

	#define PCHAR char*
	typedef char* pchar;

这两个语句都是给char*类型起一个别名，那么哪个比较好呢？ 要想知道答案，看下面：

	PCHAR p1, p2;
	pchar p3, p4;

看代码的意思，我们是想将p1,p2,p3,p4都赋成char*类型的，但是，事实是如此么？p2是么？

**注意**，p2并没有预期成为一个char*类型，因为define会在预编译阶段展开，所以语句1就相当于 char* p1, p2；而在这条语句下，p2不是一个指针，而是一个char类型的！这个错误经常会被忽略，所以一定要注意！

### 4. volatile关键字：

也许你见过这个关键字，但一般你都没有用过。哈哈，我用过！这个关键字表示改变量的值可能在外部被改变，编译器在用到这个变量时不能过度的优化，必须每次都重新从内存中读取这个变量的值，而不是将其优化在寄存器中。这个可以用来防止编译器优化产生的内存屏障，详细的看[这里](http://www.spongeliu.com/clanguage/memorybarrier/)。

好吧，32个关键字介绍完了，我自恃很了解，也从中学了不少东西，但愿下次不要再犯这种低级的错误！没关系，一切都会好起来的！Success is going from failure to failure without losing enthusiasm！

参考书目：[《C语言深度解剖:解开程序员面试笔试的秘密》](http://www.amazon.cn/gp/product/B003XF3GQO/)
