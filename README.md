# 博客搭建
## 新手入门
- 前提条件：
  - 安装 git 
  - 安装 nodejs(建议使用 Node.js 12.0 及以上版本)
  - 安装 hexo（我这里是局部安装的hexo，所以用的npx执行的相关命令，如果是全局安装就不用npx来执行hexo命令）
  - 注册GitHub账户

- （待完善）
- 建站（初始化博客）
- 更改配置文件_config.yml
- 本地启动博客预览
- 创建GitHub仓库
- 提交源代码到master
- 创建gh-pages分支（用于部署博客保存编译后的静态文件）
- 在GitHub的博客仓库设置pages的分支为gh-pages
- 将部署信息配置到博客根目录的_config.yml
- 一切都配置好之后，执行部署命令将博客部署到GitHub pages上
- 访问GitHub pages地址即可

- 注意
- 根目录下_config.yml用于当前博客全局配置
- themes下的_config.yml用于配置博客主题相关内容


### 安装 hexo
```bash
npm install hexo
```

### 初始化hexo
```bash
npx hexo init MyBlog
cd MyBlog
npm i
```

## 配置自定义主题（我这里配置的主题是 hexo-theme-matery ）
```bash
git clone https://github.com/blinkfox/hexo-theme-matery.git
```

## 配置GitHub地址
## 配置GitHub pages
## 部署博客
```bash
npm install hexo-deployer-git --save
```

```bash
npx hexo clean
npx hexo g && npx hexo d
```

- 详情参照hexo官网：https://hexo.io/zh-cn/docs/

## 博客文章特征图
博客文章特征图的存储最好是使用图床
我个人使用的是免费的图床[路过图床](https://imgse.com/)无需注册或者[img.show](https://img.show/)。
最大限制单张图片 5M，注册登录后可上传 10M 大小图片，支持 HTTPS。
## 博客评论（待更新）
配置的 valine 插件

[我的博客](https://leisuping.github.io/MyBlog/)