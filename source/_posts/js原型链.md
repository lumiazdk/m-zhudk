---
title: js原型链
abbrlink: 62868
date: 2019-05-05 10:32:43
tags:
	- 原型链
	- javaScript
categories: 前端
---
# JS原型链简单图解
## prototype和__proto__的区别
![](https://res.cloudinary.com/lumiazdk/image/upload/v1557024322/10_44_16__05_05_2019_hnkrlq.jpg)

<!-- more -->

```js
var a = {};
console.log(a.prototype);  //undefined
console.log(a.__proto__);  //Object {}

var b = function(){}
console.log(b.prototype);  //b {}
console.log(b.__proto__);  //function() {}
```
## ``__proto__``属性指向谁
![](https://res.cloudinary.com/lumiazdk/image/upload/v1557024598/WeChatcb93faa292ba7810de626eb52cb0fb14_vizfah.png)

```js
/*1、字面量方式*/
var a = {};
console.log(a.__proto__);  //Object {}

console.log(a.__proto__ === a.constructor.prototype); //true

/*2、构造器方式*/
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}

console.log(a.__proto__ === a.constructor.prototype); //true

/*3、Object.create()方式*/
var a1 = {a:1}
var a2 = Object.create(a1);
console.log(a2.__proto__); //Object {a: 1}

console.log(a.__proto__ === a.constructor.prototype); //false（此处即为图1中的例外情况）
```
## 什么是原型链

![](https://res.cloudinary.com/lumiazdk/image/upload/v1557025273/WeChat66270a736f2c22e05533c8da07192cba_jyhmjv.png)

```js
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}（即构造器function A 的原型对象）
console.log(a.__proto__.__proto__); //Object {}（即构造器function Object 的原型对象）
console.log(a.__proto__.__proto__.__proto__); //null
```

转自 [https://www.cnblogs.com/libin-1/p/5820550.html](https://www.cnblogs.com/libin-1/p/5820550.html)
