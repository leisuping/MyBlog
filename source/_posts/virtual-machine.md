---
title : 👨‍💻mac安装win10虚拟机
date: 2021-10-27
img: https://s2.232232.xyz/static/384/2022/12/08-6391846756f0e.jpg
tags:
 - Mac
 - 服务器
categories: 
 - 虚拟机
 - win10
keywords:
 - mac安装win10虚拟机
 - 服务器
---

## 一、准备下载相关工具

> 🎯 需要安装的工具：虚拟机控制中心 + win10镜像
> 1、win10镜像
> 2、Parallels Desktop
> ⚠️一定要先下载镜像然后再下载并安装控制中心！！！

## 二、下载win10镜像+控制中心

```
用迅雷下载win10镜像
ed2k://|file|cn_windows_10_business_editions_version_1909_x64_dvd_0ca83907.iso|5275090944|9BCD5FA6C8009E4D0260E4B23008BD47|/

```

> 下载虚拟机控制中心，这里分享的是icloud中的地址，也可以去官网上去下载安装
> https://www.icloud.com.cn/iclouddrive/012o3HDPl3lcQA-Ym_oLoJS9Q#Parallels_Desktop_16.5.0-49183_-_Toolbox_4.5


## 三、安装控制中心
### 1、双击下图“安装.app” 
![](./image.png)

### 2、点击删除
![](./image(1).png)

### 3、选“安装 Windows或其他操作系统（从DVD或镜像文件）”，点击继续
![](./image(2).png)

### 4、之后会扫描当前系统中的镜像文件，点击继续
![](./image(3).png)

### 5、选择windows 版本，我选的是专业版（默认是企业版）
![](./image(4).png)

### 6、取消勾选后，点击继续
![](./image(5).png)

### 7、到这里就已经安装成功了，但是我在进行下一步操作的时候又遇到了其他的问题，如图
![](./image(6).png)


## 三、配置虚拟机网络
### 1、启动虚拟机出现网络问题
![](./image(7).png)

启动虚拟机之后，由于未联网会导致初始化失败，因此需要设置虚拟机的网络与宿主机共享。

> (1)、右键访达->前往文件夹

![](./image(8).png)

> (2)、打开目录：「/Library/Preferences/Parallels/」 中的 「network.desktop.xml 文件。找到<UseKextless>-1</UseKextless> 选项，将其改为<UseKextless>0</UseKextless>，然后保存并替换！如果文件中没有这个选项，可以直接将此选项复制粘贴到 「network.desktop.xml」 文件中。

![](./image(9).png)
![](./image(10).png)

(3)、操作完成后重启虚拟机生效

具体参考[解决 PD虚拟机 网络初始化失败的方案](https://www.foxmac.com/pd-16-network-failure.html)

## 四、配置虚拟机网络
### 1、设置虚拟机与宿主机（Mac）共享网络
在虚拟机的控制中心->设置->硬件->网络
![](./image(11).png)

### 2、设置虚拟机打开模式
在虚拟机中->查看，设置打开模式：融合/全屏
![](./image(12).png)

OK啦到这里虚拟机已经安装好了，相关配置在控制中心配置就好啦，更新配置之后重启生效哦！
到这你就能在Mac上享用双操作系统啦！具体的网络代理设置可能会比较麻烦，后续遇到了再处理，补充。
![](./image(13).png)
