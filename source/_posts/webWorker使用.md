---
title: webWorker使用
tags:
  - webWorker
abbrlink: 7746
date: 2019-04-29 14:32:59
categories: 前端
---
> 在平时的运行的javascript脚本都在主线程中执行，如果当前脚本包含复杂的、耗时的代码。那么JavaScript脚本的执行将会被阻塞，甚至整个浏览器都会提示失去响应。


> 使用多线程

```js
//worker.html
var worker = new Worker('worker.js');
worker.onmessage = function (event) {
    document.getElementById("result").innerHTML += event.data + ",";
}
```

``` js
//worker.js
//省略一些代码。。。
postMessage(n);
```
