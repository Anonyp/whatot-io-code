title: "转:5-数据库分库分表(sharding)系列(四)多数据源的事务处理"
date: 2013-06-04 10:05:01
category: database
tags: database
comments: true
---

本文原文链接：http://blog.csdn.net/bluishglc/article/details/7793172 转载请注明出处！

系统经sharding改造之后，原来单一的数据库会演变成多个数据库，如何确保多数据源同时操作的原子性和一致性是不得不考虑的一个问题。总体上看，目前对于一个分布式系统的事务处理有三种方式：分布式事务、基于Best Efforts 1PC模式的事务以及事务补偿机制。我们下面对这三种处理方式一一进行分析。
<!--more-->

## 分布式事务

这是最为人们所熟知的多数据源事务处理机制。本文并不打算对分布式事务做过多介绍，读者可参考此文：关于分布式事务、两阶段提交、一阶段提交、Best Efforts 1PC模式和事务补偿机制的研究 。在这里只想对分布式事务的利弊作一下分析。

### 优势：

	1. 基于两阶段提交，最大限度地保证了跨数据库操作的“原子性”，是分布式系统下最严格的事务实现方式。
	2. 实现简单，工作量小。由于多数应用服务器以及一些独立的分布式事务协调器做了大量的封装工作，使得项目中引入分布式事务的难度和工作量基本上可以忽略不计。

### 劣势：

	系统“水平”伸缩的死敌。基于两阶段提交的分布式事务在提交事务时需要在多个节点之间进行协调,最大限度地推后了提交事务的时间点，客观上延长了事务的执行时间，这会导致事务在访问共享资源时发生冲突和死锁的概率增高，随着数据库节点的增多，这种趋势会越来越严重，从而成为系统在数据库层面上水平伸缩的"枷锁"， 这是很多Sharding系统不采用分布式事务的主要原因。

## 基于Best Efforts 1PC模式的事务

与分布式事务采用的两阶段提交不同，Best Efforts 1PC模式采用的是一阶段端提交，牺牲了事务在某些特殊情况(当机、网络中断等)下的安全性，却获得了良好的性能，特别是消除了对水平伸缩的桎酷。

[Distributed transactions in Spring, with and withoutXA](http://www.javaworld.com/javaworld/jw-01-2009/jw-01-spring-transactions.html?page=5)
一文对Best Efforts 1PC模式进行了详细的说明，该文提供的Demo代码更是直接给出了在Spring环境下实现一阶段提交的多数据源事务管理示例。
不过需要注意的是，原示例是基于spring 3.0之前的版本，如果你使用spring 3.0+,会得到如下错误：java.lang.IllegalStateException: Cannot activate transaction synchronization - already active，如果使用spring 3.0+，你需要参考
[spring-data-neo4j](https://github.com/SpringSource/spring-data-graph/blob/master/spring-data-neo4j/src/main/java/org/springframework/data/neo4j/transaction/ChainedTransactionManager.java)
的实现。鉴于Best Efforts 1PC模式的性能优势，以及相对简单的实现方式，它被大多数的sharding框架和项目采用。

## 事务补偿机制

对于那些对性能要求很高，但对一致性要求并不高的系统，往往并不苛求系统的实时一致性，只要在一个允许的时间周期内达到最终一致性即可，这使得事务补偿机制成为一种可行的方案。事务补偿机制最初被提出是在“长事务”的处理中，但是对于分布式系统确保一致性也有很好的参考意义。笼统地讲，与事务在执行中发生错误后立即回滚的方式不同，事务补偿是一种事后检查并补救的措施，它只期望在一个容许时间周期内得到最终一致的结果就可以了。事务补偿的实现与系统业务紧密相关，并没有一种标准的处理方式。一些常见的实现方式有：对数据进行对帐检查;基于日志进行比对;定期同标准数据来源进行同步，等等。

##小结

分布式事务，最严格的事务实现，但性能是个大问题;Best Efforts 1PC模式，性能与事务可靠性的平衡，支持系统水平伸缩，大多数情况下是最合适的选择;事务补偿机制，只能适用于对事务性要求不高，允许数据“最终一致”即可的系统，牺牲实时一致性，获得最大的性能回报。
