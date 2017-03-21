---
title: some features about es6
date: 2017-03-14 10:10:39
tags:
---
### 模板字符串 (tagged template strings)
[tagged template strings](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)
用``可以把字符串保存起来，保留其格式，而且使用${}能在里面执行js语法
``` bash
	var user='banana';
	var str=`this is a ${user}`;//this is a banana
```
### 箭头函数 (arrow function)
[MDN:arrow function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

##### 箭头函数的this指针
修复了一个JavaScript的设计错误
``` bash
	var obj={
		msg:'hello world',
		test:function(){
			console.log(this.msg);//'hello world'
			return function(){
				console.log(this.msg)//undefined
			}		
		}
	}
```
在箭头函数中使用则不会出现这种情况
``` bash
	var obj={
		msg: 'hello world',
		test: function(){
			return ()=>{console.log(this.msg)};//'hello world'
		}
	}
```

### 解构赋值 (destructuring assignment)
[MDN:destructuring assignment](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
比较有意思和有用的部分，可以参见上面的文档链接。
可以用于数组或者对象
### Spread syntax
[Spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)  
In ES6, you can use the ...args syntax to "spread" an array out when calling such a function. For ex
ample:
``` bash
    var numbers = [1, 1, 2, 3, 5, 8];
    var max = Math.max(...numbers);//8
```
### Rest parameters
[Rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)
``` bash
function(a, b, ...theArgs) {
  // do some thing
}
```

### 参数默认值
es6中参数可以设置默认值

``` bash

  function sayHello(greeting = "Hello", name = "CampJS") {
      console.log(`${greeting} ${name}!`);
  }

```