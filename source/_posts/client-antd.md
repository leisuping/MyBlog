---
title : antd在IE浏览器中组件不渲染
date: 2021-11-06 01:04:20
tags:
 - Vue
 - Ant Design Vue
categories: 
 - 前端
 - Vue
---
# 一、兼容性
> 🎯 Ant Design Vue 支持所有的现代浏览器和 IE9+
> 对于 IE 系列浏览器，需要提供 es5-shim 和 es6-shim 等 Polyfills 的支持
> 如果你使用了 babel，强烈推荐使用 babel-polyfill 和 babel-plugin-transform-runtime 来替代以上两个shim
> 【摘自Ant Design Vue官网】

# 二、处理方式
按照官方文档的要求，在项目中引入babel-polyfill 和 babel-plugin-transform-runtime
1. 先在项目中安装依赖包
```
npm i @babel/polyfill @babel/runtime --save
npm i  @babel/plugin-transform-runtime @babel/preset-env
npm install --save @babel/polyfill
```

2. 在main.js中引用
```
// 兼容IE浏览器
import 'core-js/stable'
import 'regenerator-runtime/runtime'
```

[Ant Design Vue官方文档地址](https://www.antdv.com/docs/vue/getting-started-cn/)
