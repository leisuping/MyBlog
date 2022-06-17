---
title: NodeJS包管理工具 n 的使用
date: 2022-06-07
img: https://s6.jpg.cm/2021/12/07/LQeKgX.jpg
tags:
 - nodejs
categories: 
 - 前端
 - nodejs
keywords:
 - nodejs
 - NodeJS包管理工具
---

## 一、NodeJS包管理工具 n 的使用

> 在做项目过程中，有时候会遇到一些依赖包只能在指定的环境下使用
> 比如：[node-sass](https://github.com/sass/node-sass/releases/tag/v4.14.1)
> 在OSX环境下nodejs版本必须在14及一下的版本才能正常使用，而官网最新稳定版目前已经是18了


出现以上这种情况为了兼容低版，我们只能通过包管理工具 n 来切换本地开发环境：nodejs版本
具体操作如下：

> 1、查看本机nodejs版本：n ls
> 2、列出nodejs官网版本：n lsr / n lsr --all
> 3、下载官网最新稳定版本：sudo n lts
> 4、下载指定版本(下载、切换nodejs版本，需要sudo执行)：sudo n install v14.19.3
> 5、切换nodejs版本：sudo n

> 通过up/down选中需要的版本，回车即可切换
> 通过 node -v 确认版本是否切换成功

简单的做个小笔记～

![](./table.jpg)

