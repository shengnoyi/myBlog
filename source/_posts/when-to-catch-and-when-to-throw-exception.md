---
title: when to catch and when to throw exception
date: 2017-05-08 10:25:15
tags: 前端开发
---
参考资料：[When to catch the Exception vs When to throw the Exceptions?](http://stackoverflow.com/questions/18679090/when-to-catch-the-exception-vs-when-to-throw-the-exceptions)
> You should catch the exception when you are in the method that knows what to do.

就是说，一个异常应该在哪里被捕获，应该取决于被捕获的地方是否能知道怎么处理这个异常，否则就应该被抛出到更上层去处理。

当然，我想知道的是，是不是所有的异常都应该被捕获和处理，应该可能出错的情况太多了，是否都值得去处理，或者某些异常就是应该只是被抛出而已。