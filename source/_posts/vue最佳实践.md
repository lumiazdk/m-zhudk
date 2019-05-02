---
title: vue最佳实践
tags:
  - vue
categories: 前端
abbrlink: 11797
date: 2019-04-29 14:01:54
---
> vue日常记录

## 释放资源
> 善用 `` destory `` 释放原生事件、第三方组件、全局事件总线等。

``` js
import bus from 'event-bus'
import plugin from 'plugin'
export default {
    // ...
    created() {
        bus.$on('hello', this.hello) // 注册全局事件
        window.addEventListener('resize', this.onResize) // DOM 事件
        plugin.init() // 第三方组件初始化
    },
    destoryed() {
        bus.$off('hello', this.hello)
        window.removeEventListener('resize', this.onResize)
        plugin.destory()
    }
}
```
<!-- more -->

## 数据请求
```js
export default {
    watch: {
        $route() {
            this.fetchData()
        },
    },
    methods() {
        fetchData() {
            // 避免重复请求
            if(this.isLoading) return
            this.isLoading = true
            // 请求数据
            // ajax...
        }
    },
    created() {
        this.fetchData()
    }
}
```
>路由跳转会触发 `` watch -> $route ``，如果是未创建的组件还会触发 `` create ``，直接 URL 只会触发 ``created`` 钩子。一般在两个位置都执行数据请求，再通过判断避免重复请求，还可以利用 ``isLoading`` 标记做加载动画。如果使用了 ``keep-alive`` 组件，还需要考虑 ``activated`` 钩子。

## 过滤器
> 过滤器的最佳应用场景应该是值的转换，比如：``Date`` 类型日期转字符串、货币、字符截断、markdown 等等。

```js
// 按长度截断文字，补...，中文 = 2
const cnReg = /[\u4e00-\u9fa5]/
Vue.filter('ellipsis', (str, len = 10) => {
    let i = 0
    let j = 0
    let ret = ''
    const text = String(str).trim()
    const max = text.length
    while (j < max && i < len) {
        const c = text.charAt(j)
        ret += c
        j += 1
        i = cnReg.test(c) ? i + 2 : i + 1
    }
    return ret === text ? text : `${ret}...`
})
// 日期转相对时间
Vue.filter('calendar', value => moment(value).calendar())
```
也可以作一些业务数据区别展示。
```js
Vue.filter('userRole', value => ['创建者', '管理员', '成员'][value])
```
## Props
> 布尔属性默认值为 ``false`` 可以省略
> 数组最好声明默认值 ``[]``，保证数据请求成功前模版里的 ``v-for``不会出错
> 对象也需要注意是否声明了默认值 ``{}``，避免模版中使用 ``obj.xx`` 报错

```js
{
    props: {
        visible: Boolen, // 默认即为 false
        data: Array,     // 需要进行非空判断
        data2: {         // 可安全使用 v-for
            type: Array,
            default: []
        },
        obj: Object,     // 需要进行非空判断
        obj2: {          // 可安全使用 obj.xx
            type: Object,
            default() {
                return {}
            }
        }
    }
}
```

