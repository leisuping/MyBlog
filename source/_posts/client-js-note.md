---
title : 用JS获取地址栏参数
date: 2021-12-23 21:04:26
img: https://s2.232232.xyz/static/384/2022/12/08-63917f85b7e7c.jpg
tags:
 - js
 - 前端
categories: 
 - 前端
 - js
keywords:
 - js
 - 用JS获取地址栏参数
---
## 一、用JS获取地址栏参数

```js
// 1. 获取地址栏完整的字符串
let urlStr = window.location.href
// 2. 获取ip协议与端口
let ip = window.location.protocol + '//' + window.location.host + '/'
let port = window.location.port
// 3. 获取参数部分
let params = window.location.search
// 4. 使用正则的方式来获取地址栏参数（推荐使用）
function getUrlQueryStr(paramsName) {
    let reg = new RegExp('[?|&]' + paramsName + '=' + '([^&;]+?)(&|#|;|$)')
    return decodeURIComponent((reg.exec(location.href) || [, ""])[1].replace(/\+/g, '%20')) || null
}
getUrlQueryStr('参数名1')
getUrlQueryStr('参数名2')
... ...
```


