---
title: Understanding Javascript prototypes
date: 2017-03-09 15:30:38
tags: Javascript
---
参考资料：  
安装node.js,npm后在命令行执行   `npm install -g planetproto`  

## exercise 2
使用`__proto__`可以在JavaScript中实现继承，不过不能用于生产环境，因为不是标准的一部分，只有部分浏览器有实现，不过作为概念理解来用还是可以的。

``` bash
var machine={
	motors:4
}

var robot={
	friendly:true
}
var robby={}

robot.__proto__=machine;

robby.__proto__=robot;

// -> What is `robby.motors`?
claim(robby.motors, 4);

// -> What is `robby.friendly`?
claim(robby.friendly, true);

```

## exercise 5
``` bash
var machine={
	parts:[],
	capabilities:{}
}
var robot={};
var vehicle={};

robot.__proto__ = machine;
vehicle.__proto__ = machine;
```
如果原型对象的字段是基本类型，比如有字段`machine.kind`，由于JavaScript属性访问的规则
（1.在自有属性中找值 ；2.在步骤1中找不到，则开始找它的原型中的属性；3.重复步骤2直至找到或者到达原型链终点也找不到，则返回`undefined`），  
此时可以调用`robot.kind`可以获得`machine.kind`的值，如果对`robot.kind`赋值，那么JavaScript会在robot对象上增加一个新的`kind`作为自有属性,覆盖了继承属性（这种覆盖称为shadow），不会影响到原型对象。
如果原型对象的字段不是基本类型，此时不会有shadow,那么修改继承对象也将会修改到原型对象
### exercise 6 (Object Create)
可以使用es5的[Object.create()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create)代替`__proto__`
##### 可以用下列方法判断原型对象是什么：
- Object.getPrototypeOf
- Object.isPrototypeOf


## exercise 8
- Constructor functions are the most used way in JavaScript to construct prototype chains. The popular
ity of constructor functions comes from the fact that this was the only original way for constructin
g types initially.
Also many engines are highly optimized for constructor functions.
- In JavaScript you can create an object like this:
``` bash
    function Alien() {
        this.age = 10;
    }

    var zippy = new Alien();
```
- When used with the keyword new functions behave like factories, meaning that they create new objects
- The new object they create is linked to the function by its prototype, more on this later. So in Jav
aScript we call this <u>an instance of the function</u>.
``` bash
    // zippy is an instance of Alien
    console.log(zippy instanceof Alien); //=> true
```
## exercise 10( The 'function prototype')

Every function in JavaScript has a special property called prototype.
``` bash
    function Alien() {}

    Alien.prototype;
```
As confusing as it may sound, this prototype property is not the real prototype (__proto__) of the f
unction.
``` bash
    Alien.__proto__ === Alien.prototype; //=> false
```
This of course generates a lot of confusion as people use the term 'prototype' to refer to different
 things.
A good clarification is to always refer to the special prototype property of functions as 'the funct
ion prototype', never just 'prototype'.

This prototype property points to the object that will be assigned as the prototype of instances cre
ated with that function when using new.

Confusing? This is easier to explain with an example:
``` bash
    function Alien(name) {
        this.name = name;
    }

    // the function Alien has a prototype property
    // we can add properties to this function prototype
    Alien.prototype.kind = 'alien';

    // when we create a new object using 'new'
    var zippy = new Alien('Zippy');

    // the __proto__ of the new object points to Alien.prototype
    zippy.__proto__ == Alien.prototype; //=> true

    // in the new object we have access to properties defined in Alien.prototype
    zippy.kind; //=> alien
```
Here is a [diagram showing this relationship](https://docs.google.com/drawings/d/1AKJcvxs0t3iGtqkRV8
rFAWlo7tojkOSwjfhr1NWaTb0/pub?w=889&h=482).

So if you create two instances of Alien, both will get Alien.prototype as their __proto__. So whatev
er you put there will be shared by the instances.

That is mostly everything there is to know about the JavaScript object model. Understanding how __pr
oto__ and function.prototype are related will give you countless hours of joy and satisfaction. Or m
aybe not.

看完了这个，我恍然大悟。

`prototype`根本不是继承意义上的原型，真正的原型应该是`__proto__`,原来只有`function`具有`prototype`这个属性  
那`prototype`这个属性有什么用呢，如果你定义了一个`function`，用这个函数去new一个对象obj之前，给这个函数的`prototype`属性加上几个子属性，例如`Alien.prototype.kind='alien'`，那么obj就会获得继承属性kind(存在于`__proto__`里)，注意与直接在`Alien`函数体内直接赋值不同。