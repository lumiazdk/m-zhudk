---
title: axios实现loading
abbrlink: 60326
date: 2019-04-29 22:58:11
tags:
  - vue
  - axios
  - loading
categories: 前端
---

> 配合element-ui的loading 使用

```js
import axios from 'axios'

import {
  Loading
} from 'element-ui'

let loading // 定义loading变量
let flag = true
function startLoading () { // 使用Element loading-start 方法
  loading = Loading.service({
    lock: true,
    text: '加载中……',
    background: 'rgba(0, 0, 0, 0.7)'
  })
}

function endLoading () { // 使用Element loading-close 方法
  loading.close()
}

let needLoadingRequestCount = 0
export function showFullScreenLoading () {
  if (needLoadingRequestCount === 0) {
    startLoading()
  }
  needLoadingRequestCount++
}

export function tryHideFullScreenLoading () {
  if (needLoadingRequestCount <= 0) return
  needLoadingRequestCount--
  if (needLoadingRequestCount === 0) {
    endLoading()
  }
}

// http request 拦截器
axios.interceptors.request.use(
  config => {
    flag = false

    setTimeout(() => {
      if (flag == false) {
        showFullScreenLoading()
      }
    }, 50)
    return config
  },
  error => {
    return Promise.reject(err)
  }
)

// http response 拦截器
axios.interceptors.response.use(
  response => {
    flag = true
    tryHideFullScreenLoading()
    return response
  },
  error => {
    return Promise.reject(error)
  }
)

```

