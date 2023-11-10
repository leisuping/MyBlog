---
title : 多个nodejs版本切换
date: 2023-11-10 9:42:24
img: https://z1.ax1x.com/2023/11/09/pi33TpT.jpg
top: true
tags:
 - nodejs
categories: 
 - nodejs
keywords:
 - nodejs
 - nvm
 - 多个nodejs版本切换
---
## 一、引文
> 🎯 nodejs是前端开发必备的环境之一，但其版本迭代更新比较迅速(当前最新的版本21.1.0)
> 所以可能会碰到运行老项目，出现当前nodejs版本过高的情况。
> 为了兼容新老项目，保证其正常运行，通常我们都会在本地安装多个版本的nodejs。今天我们就来聊聊如何安装多个版本并切换吧~




### 需要用的管理工具nvm
> nvm 是一个简单的bash脚本，主要用它来管理系统中多个nodejs版本。
> 安装：github下载地址[https://github.com/coreybutler/nvm-windows/releases]
> windows 选择安装exe后缀的即可。（安装之后会自动配置环境信息）

![](./GitHub.png)

```yml
## 安装nvm后，可查看一下版本信息
nvm -v

## nvm管理node常用命令
## 安装最新版
$ nvm install stable
## 安装指定版本的
$ nvm install [版本号]
## 切换到指定版本的node
nvm use [版本号]
## 设置默认版本
nvm alias default [版本号]
```

![](./nvmlist.png)
![](./nvmuse.png)
![](./nvm.png)

## 二、具体操作

### 1.Windows系统
#### 安装指定的nodejs版本并切换版本

```bash
## 查看nodejs版本
$ nvm list available
## 安装nodejs(我这里安装的是长期维护版本)
$ nvm install 18.18.0
## 查看已安装的nodejs版本
$ nvm list
## 切换nodejs版本
$ nvm use 18.18.0
## 查看nodejs版本
$ node -v

```


### 2.MAC系统
#### 管理工具，可以是nvm或者n

```bash
## nvm安装方式同上
## 安装n管理工具
$ npm install -g n
## 查看n是否安装成功
$ n --version
## 安装nodejs(sudo是使用的超级管理员权限)
$ n 18.18.0
$ sudo n 18.18.0
## 安装最新版本
$ n latest
## 查看已安装的版本，结果用键盘上下键，回车切换版本
$ sudo n

## 卸载已安装的版本
$ n rm 18.18.0
```





![](./hexo02.jpg)

