---
title: read the source of Vue.js
date: 2017-04-11 14:47:51
tags:
---
### 这是一篇阅读Vue.js源码的笔记。  
[Vue.js](http://cn.vuejs.org/v2/guide/)是我个人非常喜欢的一个MV*框架，上手简单，轻量化，语法设计得非常优雅，希望借此机会深入了解它的原理，也借鉴它构建一个复杂框架的代码风格，组织代码的形式。

### 阅读方法
首先按顺序看源码的，算是从底向上的方法吧，看一下粒度很细的函数是怎么实现的，然后粒度稍大点的函数怎么应用组合这些函数来完成函数的功能，然后是一个module是怎么使用这些大小函数去完成一个模块的功能，最后模块之间怎么构成一个完整的框架。  
后面会不会改，遇到不理解的地方会不会运行代码，打断点去理解，现在还不清楚，但极有可能会这么做吧。

### 有意思的函数
#### 1.cached
``` bash 
/**
 * Create a cached version of a pure function.
 */
function cached (fn) {
  var cache = Object.create(null);
  return function cachedFn (str) {
    var hit = cache[str];
    return hit || (cache[str] = fn(str))
  }
}
```
这个很有意思，可以尝试理解一下它的目的是什么。然后下面是一个它的使用实例camelize，把类似'my-component'变成'myComponent'的函数，也是源码的一部分。
``` bash
/**
 * Camelize a hyphen-delmited string.
 */
var camelizeRE = /-(\w)/g;
var camelize = cached(function (str) {
  return str.replace(camelizeRE, function (_, c) { return c ? c.toUpperCase() : ''; })
});
```
每次调用cached的时候，都会返回一个函数cachedFn，fn就是在它里面调用的。  
注意了，由于定义了一个局部变量cache（一个没有原型的空对象），每次当你调用cachedFn的时候，并不是直接去调用fn，而是先查询cache里面是否有已经计算好的值，如果有直接返回，否则在调用fn，并且存储结果到cache并返回。  
《Javascript:The Good Parts》曾经提到过这种用法，我觉得跟线性规划里面存储子问题的方式异曲同工，是提高性能的一种方法。  
#### 2.parsePath
``` bash
/**
 * Parse simple path.
 */
var bailRE = /[^\w\.\$]/;
function parsePath (path) {
  if (bailRE.test(path)) {
    return
  } else {
    var segments = path.split('.');
    return function (obj) {
      for (var i = 0; i < segments.length; i++) {
        if (!obj) { return }
        obj = obj[segments[i]];
      }
      return obj
    }
  }
}
```