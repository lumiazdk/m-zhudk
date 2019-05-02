---
title: vue中watch的详细用法
abbrlink: 44843
date: 2019-04-29 18:19:04
tags:
  - vue
  - watch
categories: 前端
---

# vue中watch的详细用法
```js
new Vue({
  el: '#root',
  data: {
    cityName: 'shanghai'
  },
  watch: {
    cityName(newName, oldName) {      // ...    }
  } 
})
```
## immediate和handler
> 这样使用watch时有一个特点，就是当值第一次绑定的时候，不会执行监听函数，只有值发生改变才会执行。如果我们需要在最初绑定值的时候也执行函数，则就需要用到immediate属性。

```js

new Vue({
  el: '#root',
  data: {
    cityName: ''
  },
  watch: {
    cityName: {
    　　handler(newName, oldName) {
      　　// ...    　　},
    　　immediate: true
    }
  } 
})
```
<!-- more -->

## deep
> 当需要监听一个对象的改变时，普通的watch方法无法监听到对象内部属性的改变，只有data中的数据才能够监听到变化，此时就需要deep属性对对象进行深度监听。

```js
new Vue({
  el: '#root',
  data: {
    cityName: {id: 1, name: 'shanghai'}
  },
  watch: {
    cityName: {
      handler(newName, oldName) {      // ...    },
    deep: true,
    immediate: true
    }
  } 
})
```

> 监听某个值

```js
watch: {    'cityName.name': {
      handler(newName, oldName) {      // ...      },
      deep: true,
      immediate: true
    }
  }
```

