---
title : sourcetree使用详解
date: 2023-03-18 24:04:20
img: https://s2.232232.xyz/static/384/2022/12/08-639182393258e.jpg
tags:
 - git
 - sourcetree
categories: 
 - 前端
 - sourcetree
keywords:
 - sourcetree
 - sourcetree使用详解
---
## 一、前言
> 🎯 最近遇到一个很奇怪的问题，使用sourcetree这么多年，第一次遇到
> 之前常用的都是使用sourcetree连接的远程仓库（Gitlab、GitHub、Gitee之类的）
> 这次是连接本地服务器私有库，在Mac上配置好信息之后使用也没有任何问题，但是在Windows上始终无法连接到服务器，呃，都不清楚是啥原因，目前正在探索中...

## 二、Tips
### 1、Sourcetree中的基本名词说明：
 克隆/新建(clone)：从远程仓库URL加载创建一个与远程仓库一样的本地仓库。
 提交(commit)：将暂存区文件提交到本地仓库 ，相当于git commit -m '提交的内容'。
 拉取(pull)：从远程仓库获取信息并同步至本地仓库，并且自动执行合并（merge）操作（git pull=git fetch+git merge）。git pull
 推送(push)：将本地仓库同步至远程仓库，一般推送（push）前先拉取（pull）一次，确保一致。git push
 获取(fetch)：从远程仓库获取信息并同步至本地仓库。
 分支(branch)：创建/修改/删除分枝。
 合并(merge)：将多个同名文件合并为一个文件，该文件包含多个同名文件的所有内容，相同内容抵消。
 贮藏(git stash)：保存工作现场。
 丢弃(Discard)：丢弃更改,恢复文件改动/重置所有改动,即将已暂存的文件丢回未暂存的文件。
 标签(tag)：给项目增添标签。
 工作流(Git Flow)：团队工作时，每个人创建属于自己的分枝（branch），确定无误后提交到master分支。
 终端(terminal)：可以输入git命令行。每次拉取和推送的时候不用每次输入密码的命令行：git config credential.helper osxkeychain sourcetree。
 检出(checkout)：切换不同分支。
 添加（add）：添加文件到缓存区。
 移除（remove）：移除文件至缓存区。
 重置(reset)：回到最近添加(add)/提交(commit)状态。
 抓取：检测远程是否有更新，如果有变动会在拉取右上角出现数字角标。从远程获取最新版本到本地 , 不会自动合并 相当于git

 [参考](https://cloud.tencent.com/developer/article/1650541)

```js
//也可以打开sourcetree右上角的git面板使用命令行提交代码
// 1.提交推送
git add file_1 file_2 //添加暂存文件
git commit -m '提交的内容' //将本地改动代码存储到本地仓库
git pull // 获取最新的代码
git push //推送至远程

// 2.贮藏工作区
git stash //存储工作区
git stash save 'msg1' // 存储工作区并备注
git stash list //展示存储区列表
git stash apply //应用存储区第一个 （不删除）
git stash pop //应用存储区第一个 （并删除）
git stash drop //删除存储区最新的内容
git stash@{0} *apply / pop / drop 第1个 ，git stash list获取下标

// 3.回撤版本
git reset --soft 34524234 // 将版本重置到34524234 版本，
git reset --soft HEAD^ 将版本重置到上一个版本
git reset --hard 34524234

// 4.其他
git remote update origin --prune // 拉取/更新远程分支
git fetch --unshallow // 拉取履历

```

### 2、git 常用操作命令
> 分支操作：
> 1.git branch //查看当前使用分支(结果列表中前面标*号的表示当前使用分支)
> 2.git branch -a //查看远程分支(列出远程分支以及本地分支名)
> 3.git branch -r  //列出远程分支(远程所有分支名)
> 4.git branch -v // 查看分支以及提交hash值和commit信息

> 1.git branch 分支名 //新建分支，本地没有就创建/获取
> 2.git checkout 分支名  //切换分支 或 检出分支
> 3.git checkout -b 分支名 //创建并切换分支，例如：git checkout -b test c6f68a8 在提交记录（c6f68a8）上创建一个名为test的分支
> 4.git branch -d 分支名 //删除分支
> 5.git branch -D 分支名 // 强制删除 若没有其他分支合并就删除 d会提示 D不会
> 6.git branch -m 旧分支名 新分支名 // 修改分支名
> 7.git branch -M 旧分支名 新分支名 // 修改分支名 M强制修改 若与其他分支有冲突也会创建(慎用)
> 8.git push  origin 分支名 // 将本地分支推送到远程
> 9.git merge 分支名 // 把该分支的内容合并到现有分支上

