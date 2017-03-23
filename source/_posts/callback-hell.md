---
title: callback hell
date: 2017-03-23 23:51:09
tags: Javascript
---
[Callback Hell](http://callbackhell.com/)

这是一篇介绍callback和如何避免走进callback hell的文章。  
callback在JavaScript里面很常见了，不多说，比较重要的是callback hell。

相比我之前看到的解决callback hell的一些文章，都是通过使用一些'新'技术，例如Promise、Generator、Es7的async/await等，这篇就显得比较优雅了。
下面进入正题：
#### 解决方案
- **Keep your code shallow**
	- 给callback hell 中的匿名函数命名
	- 利用function hoisting的特性（即可以调用函数在定义函数字面量之前，类似`function a(){}`而不是`var a=function(){}`这种格式），把函数定义写在较下方，调用写在上面  
``` bash
document.querySelector('form').onsubmit = formSubmit

function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse (err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
}
```
- **Modularize**
	- 模块化，比较通用的函数可以放在一个file里，方便其他文件调用
``` bash
module.exports.submit = formSubmit

function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse (err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
}
```
	- 调用
``` bash
var formUploader = require('formuploader')
document.querySelector('form').onsubmit = formUploader.submit
```
- **Handle every single error**
	- 处理每一个error
	- [standard](https://standardjs.com/),使用这个能够展示你的代码里的未处理的error

### 总结
大致就以上三点，其实这是一个循序渐进的过程，从callback hell->命名函数，抽象化各种操作而不是复制代码->模块化代码，复用的比较多且多个文件通用的代码可以用一个文件包含起来->npm包，多个项目通用的代码甚至可以发布npm包。  
当然，后面也有更进阶的解决方案，也就是前面讲到的Promise,generator,async/await等，但作者说这些需要在你熟悉callback这一在JavaScript编程常见的事物之后，才能用得好，**他说他自己不觉得使不使用这些东西与直接写相比有很大区别**。  
感觉这讲的不是代码之术，而是**代码之道！！！**
