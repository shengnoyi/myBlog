---
title: typeof null
date: 2017-05-08 14:56:58
tags: Javascript
---
今天翻看typeof的[文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof)，发现
``` bash
typeof null === '';//true
```
跟着索引，发现了一个有趣的小故事:[the history of "typeof null"](http://2ality.com/2013/10/typeof-null.html),原来，这是JavaScript实现的bug，事实上，typeof null应该等于'null'，但是在es6上关于修复这个bug的提议已被否决了，因为修复后可能造成现有代码奔溃。