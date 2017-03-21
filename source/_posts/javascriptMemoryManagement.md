---
title: Javascript Memory Management
date: 2017-03-09 11:52:30
tags: Javascript
---
参考资料：[Memory Management](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)  
Q:Javascript为什么要使用垃圾收集器？  
A:比较底层的程序设计语言，比如C，拥有自己内存管理的基本函数，类似malloc()和free().而在JavaScript中，变量的创建和销毁是由js引擎自动完成的，为了自动完成销毁不再需要的变量，所以需要实现garbage collector

Q:Javascript中内存变量的生命周期  
A:分配内存给变量->使用变量（计算，存储等操作）->**不再需要**该变量了，销毁变量，释放内存

### Release when the memory is not needed anymore
- Most of memory management issues come at this phase. The hardest task here is to find when "the allocated memory is not needed any longer". It often requires the developer to determine where in the program such piece of memory is not needed anymore and free it.
- High-level languages embed a piece of software called "garbage collector" whose job is to track memory allocation and use in order to find when a piece of allocated memory is not needed any longer in which case, it will automatically free it. This process is an approximation since the general problem of knowing whether some piece of memory is needed is undecidable (can't be solved by an algorithm).

怎么确定哪些是不再需要的变量，是垃圾收集中最困难的部分，而且（已经被证明？）不能用一个算法来解决，基于这个结论，下文就是阐述现有的两种接近这一目标的算法

### Reference-counting garbage collection
- This is the most naive garbage collection algorithm. This algorithm reduces the definition of "an object is not needed anymore" to "an object has no other object referencing to it". An object is considered garbage collectable if there is zero reference pointing at this object.

最先开始使用的算法，ie6,ie7就是使用该算法，如果一个对象没有任何引用指向它，则它应该被垃圾收集器收集。

基本思想是：**an object has no other object referencing to it**
circle问题：两个互相引用的对象会形成一个循环，即使在函数(可以看做javascript的scope)调用结束后，两者的仍然互相引用着，导致它们不会被垃圾收集器收集，导致内存泄露
### Mark-and-sweep algorithm
- This algorithm reduces the definition of "an object is not needed anymore" to "an object is unreachable".
- This algorithm assumes the knowledge of a set of objects called roots (In JavaScript, the root is the global object). Periodically, the garbage-collector will start from these roots, find all objects that are referenced from these roots, then all objects referenced from these, etc. Starting from the roots, the garbage collector will thus find all reachable objects and collect all non-reachable objects.

现在比较通用的算法，是假设有一个对象集合--root（global object），如果从这个对象集出发，能够引用到的，就是reachable,否则就是unreachable，会被垃圾收集器收集。

基本思想是："an object is unreachable"。    
相比较'Reference-counting garbage collection'算法，不会arithmetic上述对象形成循环而导致的内存泄露问题，在function调用结束之后，global object无法access这两个对象，所以两个对象都变成unreachable的了，会被垃圾收集器收集