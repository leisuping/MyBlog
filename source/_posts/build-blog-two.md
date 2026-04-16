---
title : 将hexo搭建的个人博客部署到GitHub
date: 2021-12-09 09:40:26
img: https://s21.ax1x.com/2024/05/16/pknW29O.jpg
top: true
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
> copy GitHub博客仓库的地址 [https://username.github.io/MyBlog](https://github.com/leisuping/)
> 在博客根目录下的 _config.yml 中配置 GitHub 仓库地址（博客部署/访问的地址）

### 1.博客部署地址相关配置
```yml
url: https://username.github.io/
root: /BlogName
permalink: :title/
permalink_defaults:
pretty_urls:
  trailing_index: true # 是否在永久链接中保留尾部的 index.html，设置为 false 时去除
  trailing_html: true # 是否在永久链接中保留尾部的 .html, 设置为 false 时去除 (对尾部的 index.html无效)
```

```yml
# 配置博客静态文件部署地址，分支
deploy:
  type: git
  repo: git@github.com: username/BlogName
  branch: gh-pages
```

### 2.博客样式相关配置

```yml
# 中文链接转拼音
permalink_pinyin:
  enable: true
  separator: '-'

# 添加emoji表情支持
githubEmojis:
  enable: true
  className: github-emoji
  inject: true
  styles:
  customEmojis:

# 博客搜索
# 安装搜索插件：npm install hexo-generator-search --save
search:
  path: search.xml
  field: post

# 安装动画插件：npm install live2d-widget-model-z16 -D
# 安装具体的动画人物：npm install --save hexo-helper-live2d 
# 配置博客动漫人物（动画跟不蒜子统计会有冲突，建议两者不要一起使用）
live2d: 
  enable: true 
  scriptFrom: local 
  pluginRootPath: live2dw/ 
  pluginJsPath: lib/ 
  pluginModelPath: assets/ 
  tagMode: false 
  log: false 
  model: 
    use: live2d-widget-model-tororo # live2d-widget-model-shizuku # live2d-widget-model-z16
  display: 
    position: right 
    width: 150 
    height: 300 
  mobile: 
    show: true 
    react: 
      opacity: 0.7
```
其他的一些配置根据主题，可能会有些略微不同，大家参考主题下的 README_CN.md 说明文件配置就好啦！

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
