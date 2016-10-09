---
title: iOS中多线程总结
date: 2016-09-03 07:56:28
tags: iOS
categories: iOS
---

在开发中多线程的使用是非常重要的，下面来梳理下多线程的相关知识<!--more-->

### 定义
说起多线程，就必须先知道线程是什么，[我是一个线程](http://mp.weixin.qq.com/s?__biz=MzAxOTc0NzExNg==&mid=416915373&idx=1&sn=f80a13b099237534a3ef777d511d831a&scene=0#wechat_redirect)这篇文章写的很不错。

可能有的人又记得进程的这个东西，他们之间的关系也很简单，一个进程可以包括多个线程，也可以看阮一峰的这篇文章[进程与线程的一个简单解释](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)。

最后来看维基百科中线程的定义：
>线程（英语：thread）是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。在Unix System V及SunOS中也被称为轻量进程（lightweight processes），但轻量进程更多指内核线程（kernel thread），而把用户线程（user thread）称为线程。
线程是独立调度和分派的基本单位。线程可以操作系统内核调度的内核线程，如Win32线程；由用户进程自行调度的用户线程，如Linux平台的POSIX Thread；或者由内核与用户进程，如Windows 7的线程，进行混合调度。
同一进程中的多条线程将共享该进程中的全部系统资源，如虚拟地址空间，文件描述符和信号处理等等。但同一进程中的多个线程有各自的调用栈（call stack），自己的寄存器环境（register context），自己的线程本地存储（thread-local storage）。
一个进程可以有很多线程，每条线程并行执行不同的任务。
在多核或多CPU，或支持Hyper-threading的CPU上使用多线程程序设计的好处是显而易见，即提高了程序的执行吞吐率。在单CPU单核的计算机上，使用多线程技术，也可以把进程中负责IO处理、人机交互而常备阻塞的部分与密集计算的部分分开来执行，编写专门的workhorse线程执行密集计算，从而提高了程序的执行效率。

### 使用
落实到日常的开发中，就没有那么抽象了，GCD是大家来完成多线程编程的首选，GCD的具体使用不是很繁琐，找了几个不错的教程，可以由浅至深的学习。

[ios多线程和GCD入门教程](https://www.raywenderlich.com/4295/multithreading-and-grand-central-dispatch-on-ios-for-beginners-tutorial)
[GCD深入理解-上(强烈推荐)](https://www.raywenderlich.com/79149/grand-central-dispatch-tutorial-swift-part-1)
[GCD深入理解-下(强烈推荐)](https://www.raywenderlich.com/79150/grand-central-dispatch-tutorial-swift-part-2)

### 合理使用
学会使用是一层，如何合理的使用需要则更加深入的理解和运用，使用GCD的优势大家都很清楚，多线程编程可以将耗时的任务放到其他线程去运行，充分利用多核处理器的优势，一定程度的提高运行的速度和流畅性，但是，缺点也不可避免，下面列出几个注意的方面：

- 大量的线程会降低代码的可读性。
- 创建和销毁线程不是没有时间和空间成本的，避免过度使用。
- 当多个线程对同一个资源出现争夺的时候要注意线程的安全的问题。
- 警惕自己有没有犯“过早优化”的错误，对不显著的性能提升花费了太长的时间。

这就是总结的GCD学习的教程了，完成多线程编程也可以通过NSOperation等更面向对象的方式来实现，朋友们可以自行选择，但选择GCD的灵活性就意味着要去精心管理它，合理选择操作的队列类型等。总之，一起来学习用好这个强大的工具吧。
