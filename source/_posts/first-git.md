---
title: 使用git上传代码至GitHub仓库
date: 2019-05-16 19:46:45
tags:
 - Git
 - GitHub
categories: 
 - 后端
 - 代码管理
keywords:
 - Git
 - 代码管理
 - 使用git上传代码至GitHub仓库
---
写在前面：看到这个标题，我想有很多没有接触过Git的大佬们肯定也是一知半解，所以我还是先来讲讲Git与GitHub的区别。之前在某个帖子上面看见那位大佬是这么总结的：git是弓，你的代码是箭，而GitHub则是靶子。我只能说两个字：精辟！哈哈！言归正传，到底什么是Git什么是GitHub呢？

## Git 版本控制

git是用于Linux内核开发的版本控制工具。与常用的版本控制工具：CVS,SVN(SubVersion)等不同，它采用了分布式版本库的方式，不必服务器端软件支持（这要分是用什么样的服务端，使用http协议或者git协议等不太一样。并且在push和pull的时候和服务器端还是有交互的。）使源代码的发布和交流及其方便。Git的速度很快这对于诸如Linux kernel这样的大项目来说自然很重要。Git最为出色的是它的合并跟踪（merge tracing）能力。

我们都知道集中式版本控制系统是必须联网才能工作，如果在局域网内还好，带宽够大，速度够快，可如果在互联网上，遇到网速慢的话，可能提交一个10M的文件就需要5分钟，很显然这种等待是非常痛苦的。分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？

比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。

和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

而 Git 的正常使用都由一些友好的脚本命令来执行，使 Git 变得非常好用，即使是用来管理我们自己的开发项目也是一个很好的选择。

## GitHub 托管


gitHub是一个面向开源及私有软件项目的托管平台，因为只支持 git 作为唯一的版本库格式进行托管故名gitHub。 
 
如前所述，作为一个分布式的版本控制系统，在Git中并不存在主库这样的概念，每一份复制出的库都可以独立使用，任何两个库之间的不一致之处都可以进行合并。GitHub可以托管各种git库，并提供一个web界面，但与其它像 SourceForge或Google Code这样的服务不同，GitHub的独特卖点在于从另外一个项目进行分支的简易性。

为一个项目贡献代码非常简单：首先点击项目站点的“fork”的按钮，然后将代码检出并将修改加入到刚才分出的代码库中，最后通过内建的“pull request”机制向项目负责人申请代码合并。已经有人将GitHub称为代码玩家的MySpace。在GitHub进行分支就像在Myspace（或Facebook…）进行交友一样，在社会关系图的节点中不断的连线。GitHub项目本身自然而然的也在GitHub上进行托管，只不过在一个私有的，公共视图不可见的库中。

开源项目可以免费托管，但私有库则并不如此。Chris Wanstrath，GitHub的开发者之一，肯定了通过付费的私有库来在财务上支持免费库的托管这一计划。通过与客户的接洽，开发FamSpam，甚至是开发GitHub本身，GitHub的私有库已经被证明了物有所值。任何希望节省时间并希望和团队其它成员一样远离页面频繁转换之苦的人士都会从GitHub中获得他们真正想要的价值。在GitHub，用户可以十分轻易地找到海量的开源代码。

说完Git与GitHub的区别我们开始步入正题，使用git上传代码至GitHub仓库。

## 前提条件：
### 要使用Git的前提当然得是你的本地安装了Git

```
安装Git：
1、进入Git官网 https://git-scm.com/
2、下载对应版本根据引导安装Git即可
3、安装完成之后执行 git --version 命令检验是否安装成功
4、安装后随处右键会出现这两个目录：Git GUI Here、Git Bash Here

```
### 拥有自己的GitHub仓库

```
1、进入GitHub官网 https://github.com/ 注册自己的GitHub账号
2、在GitHub中新建一个自己的仓库 NEW Repositories

```

## 上传代码到GitHub
### 1、要上传的项目代码

### 2、生成SSH密钥

```
在项目的根目录打开 Git Bash Here 执行一下命令
在执行命令之前可以先检验一下之前是否有配置：
git config user.name
git config user.email
如果没有配置就配置一下：
git config –global user.name '你的用户名' 
git config –global user.email '你的邮箱地址'
生成密钥：
ssh-keygen -t rsa -C '你的邮箱地址'
用户名和密码可以自己输，地址可以使用默认的（也可以直接三个回车）
密钥默认地址在 C:\用户\你电脑的用户名\.ssh 下面
id_rsa（私有秘钥）和id_rsa.pub（公有密钥）

```
### 3、将生成的SSH密钥连接上GitHub

```
1、登录GitHub进入Settings
2、进入SSH and GPG keys
3、创建New SSH key
4、粘贴你的密钥到你key输入框中，标题可以设置属于自己的标题方便查看
5、点击Add SSH key
6、最后在Git Bash Here 中输入 ssh -T git@github.com 命令测试。出现以下信息就ok啦！

Hi 007leisuping! You've successfully authenticated, but GitHub does not provide s hell access.

```

如果没有生成SSH密钥连接GitHub，在推送代码至GitHub上时会提示你需要生成SSH才能够推送代码。

### 4、执行Git命令上传代码

```
首次上传项目步骤：

第一步： cd到你的本地项目根目录下，初始化Git项目
git init
第二步：将项目的所有文件添加到仓库中，进入待提交状态
git add .
如果想添加某个特定的文件，只需把换成特定的文件名即可
第三步：将待提交的文件commit到仓库
git commit -m "提交版本信息"
第四步：将本地的项目关联GitHub上（仅用于未关联GitHub仓库的情况下）
git remote add origin GitHub仓库的地址
网址格式：git@github.com: + 用户名/ 远程仓库名（SSH形式的）
第五步：上传GitHub之前最好先pull一下
git pull origin master
第六步：上传代码到GitHub远程仓库
git push -u origin master

```


之后的代码更新可根据实际情况执行Git命令即可如：
这里如果仓库地址需要更改可执行下面的语句
git remote set-url origin 你的ssh

git pull

git add .

git commit -m '提交版本信息'

git push

......

此处附上Git一些常用命令的链接 ：https://www.jianshu.com/p/360bdda5157f
https://www.cnblogs.com/my--sunshine/p/7093412.html




