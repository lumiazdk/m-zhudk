---
title: 画江湖之this探讨
tags:
  - this
  - javaScript
  - 画江湖
top: true
categories: [前端,画江湖]
abbrlink: 43985
date: 2019-04-29 16:24:25
music:
  enable: true      # true（文章内和文章列表都显示） internal（只在文章内显示）
  server: netease   # netease（网易云音乐）tencent（QQ音乐） xiami（虾米） kugou（酷狗）
  type: song        # song （单曲） album （专辑） playlist （歌单） search （搜索）
  id: 1349292048      # 歌曲/专辑/歌单 ID
  autoplay: true
---
## 结论
> ``this`` 的指向，是在调用函数时根据执行上下文所动态确定的。

## “死记硬背”
> - 在函数体中，简单调用该函数时（非显式/隐式绑定下），严格模式下 ``this`` 绑定到 ``undefined``，否则绑定到全局对象 ``window／global``；
- 一般构造函数 new 调用，绑定到新创建的对象上；
- 一般由 ``call/apply/bind`` 方法显式调用，绑定到指定参数的对象上；
- 一般由上下文对象调用，绑定在该对象上；
- 箭头函数中，根据外层上下文绑定的 ``this`` 决定 ``this`` 指向。

<!-- more -->

## 实战例题分析
### 例题组合 1：全局环境下的 ``this``
> 这种情况相对简单直接，函数在浏览器全局环境中被简单调用，非严格模式下 ``this`` 指向 ``window``；在 ``use strict`` 指明严格模式的情况下就是 ``undefined``。我们来看例题，请描述打印结果：

```js
function f1 () {
    console.log(this)
}
function f2 () {
    'use strict'
    console.log(this)
}
f1() // window
f2() // undefined
```
> 这样的题目比较基础，但是需要候选人格外注意其变种，请再看一道题目：

```js
const foo = {
    bar: 10,
    fn: function() {
       console.log(this)
       console.log(this.bar)
    }
}
var fn1 = foo.fn
fn1()
```
> 这里 ``this`` 仍然指向的是 ``window``。虽然 ``fn`` 函数在 ``foo`` 对象中作为方法被引用，但是在赋值给 ``fn1`` 之后，``fn1`` 的执行仍然是在 ``window`` 的全局环境中。因此输出 ``window`` 和 ``undefined``，它们相当于：

```js
console.log(window)
console.log(window.bar)
```
> 还是上面这道题目，如果调用改变为：

```js
const foo = {
    bar: 10,
    fn: function() {
       console.log(this)
       console.log(this.bar)
    }
}
foo.fn()
```
> 将会输出：

```js
{bar: 10, fn: ƒ}
10
```
> 因为这个时候 ``this`` 指向的是最后调用它的对象，在 ``foo.fn()`` 语句中 ``this`` 指向 ``foo`` 对象。请记住：

> 在执行函数时，如果函数中的 ``this`` 是被上一级的对象所调用，那么 ``this`` 指向的就是上一级的对象；否则指向全局环境。


### 例题组合 2：上下文对象调用中的 ``this``
> 如上结论，面对下题时我们便不再困惑：

```js
const student = {
    name: 'Lucas',
    fn: function() {
        return this
    }
}
console.log(student.fn() === student)
```
> 最终结果将会返回 ``true``。

> 当存在更复杂的调用关系时，请看例题：

```js
const person = {
    name: 'Lucas',
    brother: {
        name: 'Mike',
        fn: function() {
            return this.name
        }
    }
}
console.log(person.brother.fn())
```
> 在这种嵌套的关系中，``this`` 指向最后调用它的对象，因此输出将会是：``Mike``。

>到此，``this`` 的上下文对象调用已经理解得比较清楚了。我们再看一道更高阶的题目：

```js
const o1 = {
    text: 'o1',
    fn: function() {
        return this.text
    }
}
const o2 = {
    text: 'o2',
    fn: function() {
        return o1.fn()
    }
}
const o3 = {
    text: 'o3',
    fn: function() {
        var fn = o1.fn
        return fn()
    }
}

console.log(o1.fn())
console.log(o2.fn())
console.log(o3.fn())

```
>答案是：``o1、o1、undefined``，你答对了吗？

>我们来一一分析。

>第一个 ``console`` 最简单，``o1`` 没有问题。难点在第二个和第三个上面，关键还是看调用 this 的那个函数。
第二个 ``console 的 o2.fn()``，最终还是调用 ``o1.fn()``，因此答案仍然是 ``o1``。
最后一个，在进行 ``var fn = o1.fn`` 赋值之后，是“裸奔”调用，因此这里的 ``this`` 指向 ``window``，答案当然是 ``undefined``。
如果面试者回答顺利，可以紧接着追问，如果我们需要让：

```js
console.log(o2.fn())
```

>输出 ``o2``，该怎么做？

>一般开发者可能会想到使用 ``bind/call/apply`` 来对 ``this`` 的指向进行干预，这确实是一种思路。但是我接着问，如果不能使用 ``bind/call/apply``，有别的方法吗？

>这样可以考察候选人基础掌握的深度以及随机应变的思维能力。答案为：

```js
const o1 = {
    text: 'o1',
    fn: function() {
        return this.text
    }
}
const o2 = {
    text: 'o2',
    fn: o1.fn
}

console.log(o2.fn())
```

> 还是应用那个重要的结论：``this`` 指向最后调用它的对象，在 ``fn`` 执行时，挂到 ``o2`` 对象上即可，我们提前进行了赋值操作。

### 例题组合 3：bind/call/apply 改变 this 指向

> 上文提到 ``bind/call/apply``，在这个概念上，比较常见的基础考察点是：``bind/call/apply`` 三个方法的区别。

>这样的问题相对基础，我们直接上答案：一句话总结，他们都是用来改变相关函数 ``this`` 指向的，但是 ``call/apply`` 是直接进行相关函数调用；``bind`` 不会执行相关函数，而是返回一个新的函数，这个新的函数已经自动绑定了新的 ``this`` 指向，开发者需要手动调用即可。再具体的 ``call/apply`` 之间的区别主要体现在参数设定上，这里不再展开。

>用代码来总结：

```js
const target = {}
fn.call(target, 'arg1', 'arg2')

```

> 相当于：

```js
const target = {}
fn.apply(target, ['arg1', 'arg2'])
```

> 相当于：

```js
const target = {}
fn.bind(target, 'arg1', 'arg2')()
```

> 具体基础用法这里不再科普，如果读者尚不清楚，需要自己补充一下知识点。

>我们来看一道例题分析：

```js
const foo = {
    name: 'lucas',
    logName: function() {
        console.log(this.name)
    }
}
const bar = {
    name: 'mike'
}
console.log(foo.logName.call(bar))
```

> 将会输出 ``mike``，这不难理解。但是对 ``call/apply/bind`` 的高级考察往往会结合构造函数以及组合式实现继承。实现继承的话题，我们会单独讲到。构造函数的使用案例，我们结合接下来的例题组合进行分析。

### 例题组合 4：构造函数和 this

> 这方面最直接的例题为：

```js
function Foo() {
    this.bar = "Lucas"
}
const instance = new Foo()
console.log(instance.bar)
```

>答案将会输出 ``Lucas``。但是这样的场景往往伴随着下一个问题：``new`` 操作符调用构造函数，具体做了什么？以下供参考：

>创建一个新的对象；
将构造函数的 ``this`` 指向这个新对象；
为这个对象添加属性、方法等；
最终返回新对象。
以上过程，也可以用代码表述：

```js
var obj  = {}
obj.__proto__ = Foo.prototype
Foo.call(obj)
```

> 当然，这里对 ``new`` 的模拟是一个简单基本版的，更复杂的情况我们会在原型、原型链相关的第2-5课《面向对象和原型——永不过时的话题》中讲述。

>需要指出的是，如果在构造函数中出现了显式 ``return`` 的情况，那么需要注意分为两种场景：

```js
function Foo(){
    this.user = "Lucas"
    const o = {}
    return o
}
const instance = new Foo()
console.log(instance.user)
```

> 将会输出 ``undefined``，此时 ``instance`` 是返回的空对象 ``o``。

```js
function Foo(){
    this.user = "Lucas"
    return 1
}
const instance = new Foo()
console.log(instance.user)
```

> 将会输出 ``Lucas``，也就是说此时 ``instance`` 是返回的目标对象实例 ``this``。

> 结论：如果构造函数中显式返回一个值，且返回的是一个对象，那么 this 就指向这个返回的对象；如果返回的不是一个对象，那么 ``this`` 仍然指向实例。


### 例题组合 5：箭头函数中的 ``this`` 指向

> 首先我们再来温习一下相关结论。

```
结论：箭头函数使用 ``this`` 不适用以上标准规则，而是根据外层（函数或者全局）上下文来决定。
```
> 来看题目：

```js
const foo = {  
    fn: function () {  
        setTimeout(function() {  
            console.log(this)
        })
    }  
}  
console.log(foo.fn())
```

> 这道题中，``this`` 出现在 ``setTimeout()`` 中的匿名函数里，因此 ``this`` 指向 ``window``对象。如果需要 ``this`` 指向 ``foo`` 这个 ``object`` 对象，可以巧用箭头函数解决：
```js
const foo = {  
    fn: function () {  
        setTimeout(() => {  
            console.log(this)
        })
    }  
} 
console.log(foo.fn())

// {fn: ƒ}
```
> 单纯箭头函数中的 ``this`` 非常简单，但是综合所有情况，结合 ``this`` 的优先级考察，这时候 ``this`` 指向并不好确定。请继续阅读。

>例题组合 6：``this`` 优先级相关
我们常常把通过 ``call、apply、bind、new`` 对 ``this`` 绑定的情况称为显式绑定；根据调用关系确定的 ``this`` 指向称为隐式绑定。

>那么显式绑定和隐式绑定谁的优先级更高呢？

>请看例题：

```js
function foo (a) {
    console.log(this.a)
}

const obj1 = {
    a: 1,
    foo: foo
}

const obj2 = {
    a: 2,
    foo: foo
}

obj1.foo.call(obj2)
obj2.foo.call(obj1)
```
> 输出分别为 ``2、1``，也就是说 ``call、apply`` 的显式绑定一般来说优先级更高。

```js
function foo (a) {
    this.a = a
}

const obj1 = {}

var bar = foo.bind(obj1)
bar(2)
console.log(obj1.a)
```

>上述代码通过 ``bind``，将 ``bar`` 函数中的 ``this`` 绑定为 ``obj1`` 对象。执行 ``bar(2)`` 后，``obj1.a`` 值为 ``2``。即经过 ``bar(2)`` 执行后，``obj1`` 对象为：{a: 2}。

当再使用 ``bar`` 作为构造函数时：

```js
var baz = new bar(3)
console.log(baz.a)
```

>将会输出 ``3``。我们看 ``bar`` 函数本身是通过 ``bind`` 方法构造的函数，其内部已经对将 ``this`` 绑定为 ``obj1``，它再作为构造函数，通过 ``new`` 调用时，返回的实例已经与 ``obj1`` 解绑。 也就是说：

> ``new`` 绑定修改了 ``bind`` 绑定中的 ``this``，因此 ``new`` 绑定的优先级比显式 ``bind`` 绑定更高。

>我们再看：

```js
function foo() {
    return a => {
        console.log(this.a)
    };
}

const obj1 = {
    a: 2
}

const obj2 = {
    a: 3
}

const bar = foo.call(obj1)
console.log(bar.call(obj2))
```

> 将会输出 ``2``。由于 ``foo()`` 的 ``this`` 绑定到 ``obj1，bar``（引用箭头函数）的 ``this`` 也会绑定到 ``obj1``，箭头函数的绑定无法被修改。

>如果将 ``foo`` 完全写成箭头函数的形式：

```js
var a = 123
const foo = () => a => {
    console.log(this.a)
}

const obj1 = {
    a: 2
}

const obj2 = {
    a: 3
}

var bar = foo.call(obj1)
console.log(bar.call(obj2))
```

> 将会输出 ``123``。

>这里我再“抖个机灵”，仅仅将上述代码的第一处变量 ``a`` 的赋值改为：

```js
const a = 123
const foo = () => a => {
    console.log(this.a)
}

const obj1 = {
    a: 2
}

const obj2 = {
    a: 3
}

var bar = foo.call(obj1)
console.log(bar.call(obj2))
```

>答案将会输出为 ``undefined``，原因是因为使用 ``const`` 声明的变量不会挂载到 ``window`` 全局对象当中。因此 ``this`` 指向 ``window`` 时，自然也找不到 ``a`` 变量了。关于 ``const`` 或者 ``let`` 等声明变量的方式不再本课的主题当中，我们后续也将专门进行介绍。

