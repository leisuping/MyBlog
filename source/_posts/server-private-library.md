---
title : 在私服创建私有库
date: 2022-06-25 22:04:06
img: https://s4.ax1x.com/2022/02/11/HUf100.png
tags:
 - server
 - 私有库
categories: 
 - server
 - repository
keywords:
 - repository
 - 如何在私服创建私有库
---
## 一、引题
> 🎯 平时编程都会有指定的仓库存储代码，除了一些公开的，比如码云、GitHub等
> 也有在公司自己的服务器上搭建的git仓库，那么我们如何将代码上传到我们自己服务器的私有库上呢？
> 具体操作如下：

## 二、将代码上传到自己服务器的私有库
### 1.服务器环境
> 安装 git
> 配置git的SSH访问

### 2.本地创建项目并上传到服务器私有库
> a、本地客户端验证连接（输入root账号的密码连接服务器）
```js
ssh root@服务器地址
```

> b、进入到指定的目录下创建私有库
```js
// 1、创建私有库：
mkdir repository.git
// 2、将私有库设置为git用户所有：
chown -R git:git repository.git
// 3、进入私有仓库：
cd repository.git
// 4、初始化git仓库：
git init - -bare

```

> c、将本地项目初始化关联并上传仓库
```js
// 进入项目根目录下初始化项目：
git init
// 关联远程仓库：
git remote add origin 远程仓库地址
// (关联上远程仓库后进入.gitignore文件下看看该忽略上传的文件目录是否已经忽略)添加所有文件(除忽略文件之外)：
git add .
// 提交到本地仓库：
git commit -m "备注信息"
// 推送至远程仓库：
git push -u origin master

```
补充：

私有库设为git用户所有：chown -R 用户:用户组 repository.git

查询文件权限：ls -l

根据端口号查看对应的进程（运行的项目）：netstat -apn|grep 6011（端口）

退出远程登录：Ctrl + D

<img src="https://s6.jpg.cm/2021/12/08/LdjvgX.png" width="40%"/>

