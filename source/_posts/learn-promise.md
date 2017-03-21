---
title: learn-promise
date: 2017-03-08 15:28:08
tags: Javascript
---
参考资料：  
 
`npm install -g promise-it-wont-hurt`


## exercise 8
``` bash

'use strict';
function attachTitle(name) {
  return 'DR. ' + name;
}

Promise.resolve('MANHATTAN')
  .then(attachTitle)
  .then(console.log);//DR.MANHATTAN

```

**Conclusion**:  
**_promise handlers_** will wrap your returned values in promises allowing additional chaining

## exercise 11
这是一个使用现有Promise接口来实现Promise.all的练习，以便了解其原理。 
### MY SOLUTION  
```bash
'use strict';

function all(action1, action2) {
    var promise = new Promise(function(fulfill, reject) {
        var counter = 0;
        var str1;
        var str2;

        action1.then((result) => {++counter;str1=result; });
        action2.then((result) => {++counter;str2=result; });
        var wait = () => {
            setTimeout(() => {
                if (counter == 2) {
                    fulfill([str1,str2]);
                } else {
                    wait();
                }
            }, 300)
        };
        wait();
    })
    return promise;
}

all(getPromise1(), getPromise2()).then(console.log);

```
### OFFICAL SOLUTION

``` bash
──────────────────
'use strict';

/* global getPromise1, getPromise2

function all(a, b) {
  return new Promise(function (fulf
    var counter = 0;
    var out = [];

    a.then(function (val) {
      out[0] = val;
      counter++;

      if (counter >= 2) {
        fulfill(out);
      }
    });

    b.then(function (val) {
      out[1] = val;
      counter++;

      if (counter >= 2) {
        fulfill(out);
      }
    });
  });
}

all(getPromise1(), getPromise2())
  .then(console.log);  
```

官方答案更加优雅吧，我的答案还用上了setTimeout,当然实际使用应该会用已有的Promise.all了。

## exercise 13(do some work)

### MY SOLUTION

``` bash  
'use strict';

var http=require('q-io/http');

http.read({url:'http://localhost:7000',method:'GET'})
.then(function (result) {
	return result.toString();
}).catch(console.error)
.then(function (userId) {
	return http.read({url:'http://localhost:7001/'+userId,method:'GET'})
})
.then(function (result) {
	console.log(JSON.parse(result));
}).catch(console.error);
```

### OFFICIAL SOLUTION
``` bash  
 var qhttp = require('q-io/http');

 qhttp.read("http://localhost:7000/")
 .then(function (id) {
   return qhttp.read("http://localhost:7001/" + id);
 })
 .then(function (json) {
   console.log(JSON.parse(json));
 })
 .then(null, console.error)
 .done();
```
大体思路上没有区别，不过我的异常处理多了一个，其实没有必要的，由于catch只能catch到被reject的promise,其他then根本不会触发，所以只需要最后一个就够了
#### 理由如下：（参见之前a importan rule那个练习）
In the code below, each expression is evaluated one after the other.  If any expression throws an exception, all subsequent expressions will not be executed and the catch block will catch and handle it.

``` bash
    try {
      doStuff()
      doMoreStuff()
    } catch (err) {
      complainAboutJavascript(err);
    }
```
With promises, we can achieve a very similar control flow as shown (assume all functions return promises):

``` bash 
    doStuff()
    .then(doMoreStuff)
    .then(null, complainAboutJavascript);
```  

Maybe we should combine the last two lines since one is a fulfill handler and the other is a rejection handler?  NO!  While this might initially seem sensible consider what would happen if doMoreStuff threw an error.  Since the promise returned from it would be rejected, it would look for the next rejection handler to handle it.

## 收获
- 理解了Promise的用法和一些实现原理和思想
- 在这个练习中学习了一个有用的npm package:[q-io](https://github.com/kriskowal/q-io#response)
与基本的fs,http等package类似，可以使用q-io/fs,q-io/http等操作，但返回的形式是Promise对象