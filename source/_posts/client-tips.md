---
title : 卑微小前端每日一个“离职”小技巧
date: 2021-12-03
img: https://s1.ax1x.com/2022/12/07/zcDD1O.jpg
tags:
 - ES6
 - css
 - js
 - 前端
categories: 
 - 前端
 - ES6
 - Tips
keywords:
 - 前端
 - ES6
 - css
---
## 一、引题
> 🎯 “不积跬步无以至千里，不积小流无以成江海”
> 每天收集一个小技巧，或许在很久的将来你也可以是大神。
> 本文主要收录一些面试的基础题，还有平时常用的一些小方法，如有描述错误请各位大佬指正！

## 二、步入正题CSS篇
> 首先，简单了解一下浏览器的渲染过程：
> 1.解析javascript，构建DOM树
> 2.解析css/style，构建渲染树
> 3.layout 布局
> 4.paint 绘制
> 5.composite 渲染层合成

### 1.如何解决高度塌陷
高度塌陷：本该在父盒子内部的元素跳到了外面
为什么会出现高度塌陷？父元素没有设置足够的大小，而子元素设置了浮动

如何解决高度塌陷的问题？
1. 简单粗暴，直接设置父元素的宽高直到适合为止。缺点：非自适应，浏览器窗口直接影响用户体验。
2. 父元素添加浮动，方便。缺点：对布局不友好，不易维护。
3. 父元素添加overflow属性
  a. overflow：auto 可能会出现滚动条，页面不美观
  b. overflow：hidden 可能会给内容带来不可见问题
4. 父盒子里最下方引入清除浮动块，最简单的：<br style="clear:both;"/> 缺点：引入了冗余的代码块
5. 父盒子设置after伪类，在伪类中添加clear：both 属性，清除浮动。（推荐）
```html
#parent:after{
    clear: both;
    width: 0;
    height: 0;
    display: block;
    visibility: hidden；
}
```
> 补充：清除浮动伪类有.clearfix clear:both overflow:hidden
> 常用：.clearfix

### 2.CSS显示与隐藏的实现方式有哪些？区别在哪？
实现方式：
a.display[none]：不占位隐藏，会导致页面回流重绘，绑定事件无效
b.visibility[hidden]：占位隐藏，可能会导致页面重绘，绑定事件无效
c.opacity[设置透明度为0]：内部元素都会受影响，可以被继承，绑定事件仍有效
d.rgb[设置透明度]：只作用于当前元素，不可被继承


### 3.使用css画三角形

```html
<div id="mydiv"></div>

<style>
    /* 设置div边框颜色（上、右、下、左）下边框为红色，其他边框设为透明 */
    #mydiv {
        width: 0;
        height: 0;
        border: 15px solid;
        border-color: transparent transparent red transparent;
    }
</style>
```
效果图：<div id="mydiv"></div>
<style>
    #mydiv {
        width: 0;
        height: 0;
        border: 20px solid;
        border-color: transparent transparent red transparent;
    }
</style>

### 4.水平/垂直居中
水平居中：
行内元素水平居中 text-align:center;

块级元素水平居中
定宽（width固定）设置 margin:0 auto 即可;
不定宽：
a. 设置display:flex,justify-content:center;
b. 设置display:table,且margin:0 auto
c. 设置position + transform 或者position + margin:0 auto;等

垂直居中：
单行元素垂直居中：
a. 设置行高=高 line-height = height;
b. 设置上下内边距相等 paddingtop = paddingbottom;
块级/多行元素垂直居中：
a. 设置父元素 display: flex;align-items: center;
b. 设置父元素 display: table-cell;vertical-align: middle;
c. 设置父元素 position的属性值等等

### 5.水平垂直居中
推荐 position定位 与 flex布局

```html
<style>
/* 设置父元素display属性值为flex，并设置子元素水平居中 */
#parent {
    display: flex;
    justify-content: center;
    align-items: center;
}

/* 或者设置元素本身position的属性值为absolute */
#mydiv {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
}
</style>
```

### 6.左中右布局实现方式

1. 浮动布局，左浮动，右浮动，中间自动填充。
2. 绝对定位 position
  a. 左边元素position：absolute；left：0；
  b. 右边元素position：absolute；right：0；
  c. 中间元素position：absolute；left：300px;right：300px;
3. flex布局
  a. 父元素display: flex；
  b. 左右子元素width: 300px;
  c. 中间子元素flex: 1;
4. table布局
  a. 父元素width: 100%;display: table;
  b. 左右子元素display: table-cell;width: 300px;
5. grid布局(设置父元素)
width: 100%;
display: grid;
grid-template-rows: 100;
grid-template-columns: 300px auto 300px;

### 7.一行文本超出显示...
```
overflow: hidden;
text-overflow:ellipsis;
white-space: nowrap;
```

### 8.多行文本超出显示...
```
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
```

## 三、步入正题js篇

### 1.数组相关操作方法
● push 在数组末尾添加元素
● pop 删除末尾元素
● unshift 在数组首位添加元素
● shift 删除首位元素
● splice 删除数组元素，或者在指定位置插入元素
● sort 数组排序
● revese 数组倒序（排序）

普通数组的查询：
● indexOf 从数组的第一位开始向后查找指定的值并返回值的位置
● lastIndexOf 从数组的最后一项向前查找指定的值并返回值的位置（下标）找不到返回-1
对象数组的查询：
● findIndex 从数组的第一位开始向后查找指定的值并返回值的位置
● filter 遍历数组，返回符合条件的新数组
● find 遍历数组，返回符合条件的对象
● every 遍历数组，如果有一项不符合条件就返回false (如果数组每项都符合条件，就返回true)
● some 遍历数组，如果有一项或多项符合条件就返回true 
● splice 可做删除、插入、替换
● concat 追加元素到数组的末尾，返回一个新的数组（可合并两个数组）

### 2.字符串相关操作方法
● includes() 字符串中是否包含某个字符串，返回true/false
● repeat(number) 得到一个重复number次的字符串
● startsWith() 字符串是否为某个字符串开始，我一般用它判断url是否有http
● endsWith() 字符串是否为某个字符串结尾，判断后缀名
● 'abc'.padEnd(5, '0'); // abc00; 用给定的字符串在尾部拼接到指定长度。第一个参数为长度，第二个参数为用于拼接的值。
● 'abc'.padStart(5, '1'); // 11abc; 用给定的字符串在首部拼接到指定长度。第一个参数为长度，第二个参数为用于拼接的值。首位补0？
● trim()、trimStart()、trimEnd()去空格

### 3.??合并空运算符
```js
const test = '';

console.log(test || 'haha'); // haha;
console.log(test ?? 'hah'); // '';
```

[ES6官方文档](https://es6.ruanyifeng.com/#docs/string-methods)


> 害！今天就先写到这里吧！后期再慢慢更新

![](./images.jpeg)


