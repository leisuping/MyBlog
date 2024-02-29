---
title : 微信小程序npm构建报错，无法生成miniprogram_npm
date: 2023-11-09 8:32:24
img: https://s11.ax1x.com/2024/02/29/pFwtTsI.jpg
tags:
 - nodejs
categories: 
 - nodejs
keywords:
 - nodejs
 - npm
 - 多个nodejs版本切换
---
## 一、随记
> 🎯 问题：微信小程序在初始化项目后构建npm没有生成miniprogram_npm文件

### 解决办法
> 1、初始化npm init
> 2、安装依赖 npm install XXXX
> 3、设置project.config.json文件

```bash
## 初始化npm init
$ npm init
## 安装依赖 npm install XXXX
$ npm i

```

> 设置好project.config.json文件之后再次构建npm
> 开发者工具–>工具–>构建npm 就会生成 miniprogram_npm 文件夹了

```yml
## project.config.json
"packNpmManually": false,
"packNpmRelationList": [
    {
    "packageJsonPath": "./package.json",
    "miniprogramNpmDistDir": "./"
    }
]

```

> 页面中引入组件使用地址路径

```json
{
  "navigationBarTitleText": "测试页面",
   "usingComponents": {
    "van-image": "@vant/weapp/image/index",
    "van-tag":"@vant/weapp/tag/index",
    "van-cell": "@vant/weapp/cell/index",
    "van-cell-group": "@vant/weapp/cell-group/index",
    "van-row": "@vant/weapp/row/index",
    "van-col": "@vant/weapp/col/index"
  }
}

```