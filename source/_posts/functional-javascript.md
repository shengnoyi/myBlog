---
title: functional javascript
date: 2017-03-10 16:18:40
tags: Javascript
---

##### 函数式编程，我觉得是比较难以理解的一个练习，可能之后需要反复看。

参考资料：  
`npm install -g functional-javascript`  
[MDN: Array.prototyp.reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)  
[Tail calls, @tailrec and trampolines](http://blog.richdougherty.com/2009/04/tail-calls-tailrec-and-trampolines.html)
### exercise 6:reduce
``` bash
 function countWords(arr) {
   return arr.reduce(function(countMap, word) {
     countMap[word] = ++countMap[word] || 1 // increment or initialize to 1
     return countMap
   }, {}) // second argument to reduce initialises countMap to {}
 }

 module.exports = countWords
```
### exercise 7:recrusion
使用递归实现一个类似reduce的函数:reduce(arr,fn,initial)

``` bash
function reduce(arr, fn, initial) {
  return (function reduceOne(index, value) {
    if (index > arr.length - 1) return value // end condition
    return reduceOne(index + 1, fn(value, arr[index], index, arr)) // calculate & pass values to next step
  })(0, initial) // IIFE. kick off recursion with initial values
}

module.exports = reduce

```
这道感觉有点难，没有做出来，再斟酌一下吧

### exercise 9:partial application without bind



``` bash
    var slice = Array.prototype.slice

    function logger(namespace) {
      return function() {
        console.log.apply(console, [namespace].concat(slice.call(arguments)))
      }
    }

    module.exports = logger

```

考究call 和 apply的使用，console对象有点意思。

### exercise 10：artial application with bind

``` bash 
  module.exports = function(namespace) {
    return console.log.bind(console, namespace)
  }
```
bind函数用法
[MDN:Function.prototype.bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

### exercise 13:Blocking Event Loop
``` bash

 function repeat(operation, num) {
   if (num <= 0) return

   operation()

   // release control every 10 or so
   // iterations.
   // 10 is arbitrary.
   if (num % 10 === 0) {
     setTimeout(function() {
       repeat(operation, --num)
     })
   } else {
     repeat(operation, --num)
   }
 }

 module.exports = repeat

```

使用setTimeout交出程序控制权，插入事件队列中

### exercise 14:tranpoline
越来越看不懂了,使用tranpoline的优点似乎是不会爆栈
可以看看这篇：[Tail calls, @tailrec and trampolines](http://blog.richdougherty.com/2009/04/tail-calls-tailrec-and-trampolines.html)
题目：  
The boilerplate includes a definition of repeat. repeat will take a Function ope
ration, and a Number num, and invoke operation num times:
``` bash
    var count = 0
    repeat(function() {
      count++
    }, 100)

    console.log('executed %d times.', count)
    // => executed 100 times.
```
BUT note that executing repeat with a large num causes a stack overflow:
``` bash
    var count = 0
    repeat(function() {
      count++
    }, 100000)

    console.log('executed %d times', count)
    // => RangeError: Maximum call stack size exceeded
```
#### Task

Modify the boilerplate below such that it uses a trampoline to continuously call
 itself synchronously.

You can assume that the operation passed to repeat does not take arguments (or t
hey are already bound to the function) and the return value is not important.

###### Conditions

  * Do not change the implementation of repeat to include any loops(you may chan
ge it in other ways though).

##### Hints

  * Modify `repeat` so it returns the 'next step', if there is one.
  * A trampoline continues to synchronously execute steps, getting new steps, un
til there are no more steps. You can use a loop here!
  * If your program takes a long time to run, something is probably wrong.  Use
Control - C to kill the node process.

##### Boilerplate
``` bash
    function repeat(operation, num) {
      // Modify this so it doesn't cause a stack overflow!
      if (num <= 0) return
      operation()
      return repeat(operation, --num)
    }

    function trampoline(fn) {
      // You probably want to implement a trampoline!
    }

    module.exports = function(operation, num) {
      // You probably want to call your trampoline here!
      return repeat(operation, num)
    }
```
答案：
``` bash 

 function repeat(operation, num) {
   return function() {
     if (num <= 0) return
     operation()
     return repeat(operation, --num)
   }
 }

 function trampoline(fn) {
   while(fn && typeof fn === 'function') {
     fn = fn()
   }
 }

 module.exports = function(operation, num) {
   trampoline(function() {
     return repeat(operation, num)
   })
 }
```

(太困难了，感觉目前很难运用，先到这里吧，待填坑...)