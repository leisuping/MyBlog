---
title : 微信小程序父组件调用子组件的方法
date: 2022-06-15 13:05:16
img: https://s6.jpg.cm/2021/12/07/LQeyXp.png
tags:
 - 小程序
categories: 
 - 小程序
keywords:
 - 小程序
 - 微信小程序父组件调用子组件的方法
---
## 一、微信小程序父组件调用子组件的方法

### 1.在子组件的methods里定义方法
```js
Component({
    data: {
        selectSizeShow:false
    },
    properties: {... ...},
    methods: {
        selectSizeshowPopup() {
            this.setData({ selectSizeShow: true });
        },
        selectSizeonClose() {
            this.setData({ selectSizeShow: false });
        },
    },
})
```

### 2.父组件调用
> 在wxml里定义一个id唯一标识
> showSelectSize 用来触发子组件的方法
> 父组件index.js中通过id标识获取子组件对象，并调用子组件的方法
```js
<selectSize id="selectSize" options="{{optionsSize}}" atSize="{{atSize}}"  bind:switchSize="switchSize"></selectSize>
<van-icon name="setting-o" class="headerIcon" bindtap="showSelectSize"/>

```

```js
Page({
    data: {... ...},
    // 激活切换分辨率/尺寸的组件
    showSelectSize() {
        var selectSizeObj = this.selectComponent("#selectSize")
        console.log(selectSizeObj.data) //子组件的数据
        selectSizeObj.selectSizeshowPopup() //子组件的方法
    },
})

```