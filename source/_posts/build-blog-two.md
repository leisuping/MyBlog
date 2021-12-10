---
title : 将hexo搭建的个人博客部署到GitHub
date: 2021-12-09 09:40:26
img: https://s6.jpg.cm/2021/12/07/LQegeR.jpg
tags:
 - hexo
 - 部署博客
categories: 
 - nodejs
 - hexo
keywords:
 - hexo
 - 将hexo搭建的个人博客部署到GitHub
---
## 一、新建仓库
> 🎯 上一篇[如何使用hexo搭建个人博客](https://leisuping.github.io/MyBlog/build-blog-one/)中准备环境有提到要注册GitHub/GitLab账号
> 今天我们要登录GitHub新建博客仓库，准备将博客部署到GitHub Pages

![](./github01.jpg)

1.登入 github 账号
2.New repository 在 github 新建一个名为：username用户名.github.io 的 Repository（我这里在用户名下建了一个与本地博客同名的目录）
3.注意要公开仓库，私有库是无法访问的（GitHub Pages 在公共存储库中可用）
4.建议选择使用 README 初始化此存储库

![](./github02.jpg)


## 二、配置博客
> copy GitHub博客仓库的地址 https://username.github.io/MyBlog
> 在博客根目录下的 _config.yml 中配置 GitHub 仓库地址（博客部署/访问的地址）

```yml
url: https://username.github.io/
root: /MyBlog
permalink: :title/
permalink_defaults:
pretty_urls:
  trailing_index: true # 是否在永久链接中保留尾部的 index.html，设置为 false 时去除
  trailing_html: true # 是否在永久链接中保留尾部的 .html, 设置为 false 时去除 (对尾部的 index.html无效)
```

## 三、上传部署博客
> 1.将本地的 myblog 与 GitHub仓库关联（配置ssh），将代码提交到仓库的 master分支下
> 2.在博客仓库下新建 gh-pages 分支，用来存储博客编译后要部署的静态文件
> 3.在仓库下配置发布源 Settings => Pages 在 GitHub Pages 下设置发布源分支 gh-pages
> 4.在 _config.yml 中配置博客部署信息

![](./githubPages.jpg)

配置博客部署信息
```yml
deploy:
  type: git
  repo: git@github.com:username/MyBlog
  branch: gh-pages

search:
  path: search.xml
  field: post
```

配置好之后，重新构建博客生成静态文件并部署到 GitHub Pages

```bash
$ npx hexo clean
$ npx hexo g
$ npx hexo d
```

![](./hexo02.jpg)

部署成功之后就可以直接访问你的博客啦！
[https://username.github.io/MyBlog](https://leisuping.github.io/MyBlog/)

<img src="https://s6.jpg.cm/2021/12/08/LdjVpU.gif" width="200px"/>
