---
title : Element自定义button图标
date: 2021-11-08
tags:
 - Vue
 - ElementUI
categories: 
 - 前端
 - Vue
 - ElementUI
keywords:
 - 前端
 - ElementUI
 - 自定义button图标
---
## 一、前提
> 🎯 ElementUI中icon图标有限，无法满足所有项目的需求。
> API文档中的 Button 都是设置icon属性或者直接用<i class="xxx"></i>标签来定义按钮的图标，这是使用element提供的icon常规操作。
> 那么，如果需要引用其他图标该怎么处理呢？最近做的项目中就有用到，按钮需要自定义图标，我是采用了以下方式来处理的（不是最优，但可满足项目需求）

## 二、处理方式
### 1. 官网API提供的图标按钮
![](./img1.jpg "ElementUI图标按钮")

### 2. 自定义icon样式
> 1、自定义一个搜索图标按钮
> 2、定义一个 el-icon-myself-search 类属性，将自定义（外部）的图片设置背景图
> 3、在 el-icon-myself-search:before 伪类中定义 content 文本占位（最好是一个汉字这样图标大小正好，我这里用Unicode转码了）
> 4、在伪类中设置 visibility 属性隐藏文字

```css
/* 查询按钮 */
.el-icon-myself-search{
  background: url('./assets/assets/ic_按钮_搜查.png') no-repeat;
  font-size: 14px;
  background-size: cover;
}
.el-icon-myself-search:before{
  content: "\66ff";
  font-size: 14px;
  visibility: hidden;
}
```

### 3. 在页面中引用自定义的icon
```
<el-button type="primary"
    icon="el-icon-myself-search"
    @click="handelSearch">查询</el-button>
```
### 4. 效果预览
![](./img2.jpg)

