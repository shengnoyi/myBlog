---
title: JavaScript语言精粹
date: 2017-02-20 00:41:19
tags: JavaScript语言精粹
---
最近在补JavaScript的基础知识，有人推荐了[JavaScript语言精粹](https://book.douban.com/subject/3590768/)给我，刚读了40几页，觉得大有收获，把构造器，闭包的原理和应用讲的很清晰，确实是本不可多得的读物。

# Javascript语言精粹

## lambda语言

## LISP语言

## JSLint

## 函数
## 函数对象
每个函数对象在创建时也随带这一个prototype属性，它的值是拥有**constructor属性且值为该函数的对象**
### 函数调用
#### 在Js中有四种函数调用模式，不同模式将会影响this的值
- 方法调用模式
	- Object.method()
- 函数调用模式
	- 当一个函数并非一个对象的属性时，那么它将被当做一个函数来调用
	- 当函数以此模式调用时，
		- **this将被绑定到全局变量变量，这是设计上的一个错误。**
		- **<u>倘若设计正确</u>，那么内部函数被调用时，this仍然应该绑定到外部函数的this变量,所以es2015的箭头函数解决了这个问题！！！**
- 构造器调用模式
``` bash
	//Quo.js
	//创建一个名为Quo的构造器函数。
	var Quo=function(str){this.status=str};
	Quo.prototype.getStatus=function(){return this.status};
	var myQuo=new Quo('confused');
	alert(myQuo.getStatus());
```
- apply调用模式