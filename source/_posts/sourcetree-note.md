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
1、Sourcetree中的基本名词说明：
 克隆/新建(clone)：从远程仓库URL加载创建一个与远程仓库一样的本地仓库。
 提交(commit)：将暂存区文件上传到本地代码仓库。
 推送(push)：将本地仓库同步至远程仓库，一般推送（push）前先拉取（pull）一次，确保一致（十分注意：这样你才能达到和别人最新代码同步的状态，同时也能够规避很多不必要的问题）。
 拉取(pull)：从远程仓库获取信息并同步至本地仓库，并且自动执行合并（merge）操作（git pull=git fetch+git merge）。
 获取(fetch)：从远程仓库获取信息并同步至本地仓库。
 分支(branch)：创建/修改/删除分枝。
 合并(merge)：将多个同名文件合并为一个文件，该文件包含多个同名文件的所有内容，相同内容抵消。
 贮藏(git stash)：保存工作现场。
 丢弃(Discard)：丢弃更改,恢复文件改动/重置所有改动,即将已暂存的文件丢回未暂存的文件。
 标签(tag)：给项目增添标签。
 工作流(Git Flow)：团队工作时，每个人创建属于自己的分枝（branch），确定无误后提交到master分支。
 终端(terminal)：可以输入git命令行。
 每次拉取和推送的时候不用每次输入密码的命令行：git config credential.helper osxkeychain sourcetree。
 检出(checkout)：切换不同分支。
 添加（add）：添加文件到缓存区。
 移除（remove）：移除文件至缓存区。
 重置(reset)：回到最近添加(add)/提交(commit)状态。

[参考](https://cloud.tencent.com/developer/article/1650541)
