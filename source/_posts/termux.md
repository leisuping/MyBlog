---
title : 使用termux在安卓系统上搭建网站
date: 2023-12-14 16:22:26
img: https://s11.ax1x.com/2023/12/15/pihzLZt.jpg
tags:
 - termux
categories: 
 - nodejs
 - termux
keywords:
 - termux
 - 使用termux在安卓系统上搭建网站
---
## 一、介绍
> 🎯 “Termux 是一款 Android 终端模拟器和 Linux 环境应用程序，无需 root 或设置即可直接运行。自动安装最小的基本系统，使用包管理器可以使用其他包。”
> 它就是一个普通的APP，可以在手机的应用商城下载安装，不需要任何配置，打开即用。


## 二、准备环境

### 1.安装 Termux 
> 在手机的应用商城下载并安装，打开后是一个全屏的命令面板。
> 1.更新系统

```bash
# 连接远程仓库，获取软件包信息
$ apt update

# 更新本地已经安装的软件包
$ apt upgrade
```

> 2.测试系统

```bash
# 安装 sl 软件包
$ apt install sl

# 运行(正常会显示一个火车的命令行动画)
$ sl
```

> 3.访问本机存储

```bash
# 访问本机存储，手机会弹出对话框，询问是否允许 Termux 访问手机存储，点击"允许"。
# 之后会在当前目录生成storage子目录，它是手机存储的符号链接，下载文件就是到这个目录去下载。
$ termux-setup-storage
```


Termux除了封装了apt命令，还封装了pkg一些基础命令，比如：
```bash
pkg search <query>              # 搜索包
pkg install <package>           # 安装包
pkg uninstall <package>         # 卸载包
pkg reinstall <package>         # 重新安装包
pkg list-all                    # 列出可供安装的所有包
pkg list-installed              # 列出已经安装的包
pkg show <package>              # 显示某个包的详细信息
pkg files <package>             # 显示某个包的相关文件夹路径
pkg update                      # 更新源（自动切换源）
pkg upgrade                     # 升级软件包
```

> 4.安装nodejs
```bash
$ apt install nodejs
```

(未完待续...)

