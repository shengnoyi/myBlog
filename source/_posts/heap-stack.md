---
title: heap & stack
date: 2017-03-08 11:35:33
tags: 数据结构
---
参考资料：  
[Memory : Stack vs Heap](http://gribblelab.org/cbootcamp/7_memory_stack_vs_heap.html)，来自一个C语言的入门教程  
[谈谈javascript语法里一些难点问题"](http://blog.jobbole.com/81010/)



Q:栈是什么？  
>The stack is a "LIFO" (last in, first out) data structure, that is managed and optimized by the CPU quite closely.

- 栈区的生命周期：_A key to understanding the stack is the notion that **when a function exits, all of its variables are popped off of the stack (and hence lost forever)**._
- 栈区大小的限制：_Another feature of the stack to keep in mind, is that there is **a limit (varies with OS) on the size of variables** that can be store on the stack. This is not the case for variables allocated on the heap._

Q:栈有什么优点？
>The advantage of using the stack to store variables, is that memory is managed for you. You don't have to allocate memory by hand, or free it once you don't need it any more. What's more, because the CPU organizes stack memory so efficiently, reading from and writing to stack variables is very fast.  



Q:栈用在什么地方？  
在c中，那些不用我们自己分配的变量，比如int,double这些变量，都是直接使用栈区的值，而那些需要分配的值，栈区存储的值只是它堆区的引用地址，通过这个地址来获得堆区的对象。

![](http://ww3.sinaimg.cn/mw690/7178f37ejw1en4hc7qgvcj20fp084jrr.jpg)
图片

Q:堆是什么？
>The heap is a region of your computer's memory that is not managed automatically for you, and is not as tightly managed by the CPU.

Q:堆有什么优点？
>Unlike the stack, the heap does not have size restrictions on variable size (apart from the obvious physical limitations of your computer).

Q:堆和栈有什么区别？
### Stack
- very fast access
- don't have to explicitly de-allocate variables
- space is managed efficiently by CPU, memory will not become fragmented
- local variables only
- limit on stack size (OS-dependent)
- variables cannot be resized
### Heap
- variables can be accessed globally
- no limit on memory size
- (relatively) slower access
- no guaranteed efficient use of space, memory may become fragmented over time as blocks of - memory are allocated, then freed
- you must manage memory (you're in charge of allocating and freeing variables)
- variables can be resized using realloc()

## 继续深入
待填坑...

[What and where are the stack and heap?](http://stackoverflow.com/questions/79923/what-and-where-are-the-stack-and-heap)

[Mozilla Foundation:《Javascript Memory Management》](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)  

关于JavaScript的内存管理请参见另一篇博文[Javascript Memory Management](https://shengnoyi.github.io/2017/03/09/javascriptMemoryManagement/)