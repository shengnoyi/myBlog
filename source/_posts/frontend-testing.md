---
title: Frontend Testing
date: 2017-09-15 10:48:39
tags:
---

### assert

[assert module](http://javascript.ruanyifeng.com/nodejs/assert.html)
``` bash javascript
var assert = require('assert')
function add(a, b) {
  return a + b
}

assert(add(1, 2) === 3, 'add(1, 2) should be 3')
```