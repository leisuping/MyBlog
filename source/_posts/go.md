---
title : go语言环境搭建及本地项目运行笔记
date: 2023-03-16 18:04:26
img: https://s2.232232.xyz/static/384/2022/12/08-6391832fa8506.jpg
top: true
tags:
 - go
categories: 
 - nodejs
 - go
keywords:
 - go
 - go语言环境搭建
---
## 一、引题
> 🎯 昨天弄了一下go 今天稍稍整理一下，做个笔记。
> 准备环境可以参考一下，运行项目啥的可以忽略，因为可能对其他公司项目并不适用（服务器上的环境不同）

## 二、准备环境
> 安装 [go](https://golang.google.cn/dl/) 
> 配置系统环境变量
> 配置 GOPROXY 环境变量


<img src="./001.png" width="98%" title="下载安装包"/>

```shell
# 安装成功之后，查看版本
go version

```

<img src="./004.png" width="98%" title="查看go版本"/>

<img src="./002.png" width="98%" title="配置环境变量"/>


配置 GOPROXY 环境变量，[可参考官方文档](https://learnku.com/go/wikis/38122)
```shell
# 启用 Go Modules 功能
go env -w GO111MODULE=on

# 配置 GOPROXY 环境变量，以下三选一
# 1. 七牛 CDN
go env -w  GOPROXY=https://goproxy.cn,direct

# 2. 阿里云
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/,direct

# 3. 官方(个人推荐使用官方镜像)
go env -w  GOPROXY=https://goproxy.io,direct

# 配置成功之后，查看一下是否配置成功
go env

```

<img src="./003.png" width="98%" title="查看go环境配置"/>



## 三、运行本地项目
> 在go语言环境都搭建好的情况下，这里运行一下本地的项目（可忽略）
> 因为我本地的项目，使用的框架版本特殊，因此有些操作需要手动替换一下框架包

```js
// 初始化go.mod (如已有.mod文件则忽略)
go mod init

// 安装一个项目下的所有依赖 (建议配置好镜像再进行依赖下载)
go get -v ./...

// go命令手动加载所有的安装包
go get -d -v ./...

// 启动项目
go run 项目入口文件.go
```

异常1：
会出现app.StaicWeb之类的语法错误，是因为你iris依赖的版本和代码不匹配，公司项目用的一点几版本，目前最新是二点几版本。
解决：
1、命令行下载服务器上的依赖：scp root@tiny.fndroid.com:/home/go-workspace.tar .
2、命令行go env 找到你的GOPATH路径
3、把GOPATH路径目录里的pkg\mod\github.com\katarars 的iris包替换掉（嗯，我这里是直接将get下来的包重命名了，然后将pkg\mod\github.com\katarars 的iris包重命名为get下来的包名）
4、再次运行go run main.go会报路径上的错（是因为mod装的依赖原因，服务器没用mod）
5、把main.go文件里的 handler "./handler" 改成 "项目文件名/main.go"
6、再次运行go run main.go即可

项目部署:
1、项目运行成功之后，可以在本地进行改动，改动完之后提交代码至仓库
2、ssh上服务器，到对应的项目目录下，拉取最新的代码，然后编译，重启即可
```js
// 示例
// 拉取最新的代码
git pull
// 编译项目（打包）
go build wifi.go
// 重启
pm2 restart wifi
```
