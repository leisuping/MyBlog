---
title : 前端系统添加水印
date: 2021-12-01
tags:
 - Vue
 - elementUI
 - 水印
categories: 
 - 前端
 - Vue
 - js
keywords:
 - 前端
 - Vue
 - 前端系统添加水印
---

<img src="./images.jpeg" width="90%"/>

## 一、需求
> 🎯 系统中重要数据的导出文件都有水印，而页面截图，是一个漏洞。
> 为了防止截图泄漏数据，前端添加水印，一旦，截图的数据泄漏，可以根据图片上的水印，追究到人。
> 本文使用的是前端方案：显性水印 + Canvas（前端方案可以减少服务器压力，快速响应页面）
> 注：对于安全度要求不是那么高的情况这种前端方案是可行的，但是对于安全度要求比较高建议采取其他方案比如后端处理（低透明度隐藏水印、傅里叶大法等）

## 二、实现

> 1.定义watermark.js
> 2.设置画板的 pointerEvents 属性为none，页面事件穿透。
> 3.利用画板 canvas 的 toDataURL 方法设置水印，同时设置透明度，将画板的 z-index 设为 99999（置顶）
> 4.调用watermark中的方法，实现前端系统页面添加水印

1.新建watermark.js
通过Canvas绘画，避免了在水印密度较大的情况下大量DOM元素的创建与添加
且用户无法通过浏览器开发者模式禁用元素来去除掉水印遮罩

```js
// 初始化 watermark
let watermark = {};

// 封装设置系统水印方法
let setWatermark = (str) => {
  let id = '3.141592653589793.3.141592653589793';

  let watermarkOld = document.getElementById(id);
  if (watermarkOld) {
    document.body.removeChild(watermarkOld);
  }

  let can = document.createElement('canvas');
  can.width = 320;
  can.height = 120;

  let cans = can.getContext('2d');
  if (!cans) return;

  cans.rotate(-20 * Math.PI / 180);
  cans.font = '15px Vedana';
  cans.fillStyle = 'rgba(199, 199, 199, 0.256)';
  cans.textAlign = 'left';
  cans.textBaseline = 'Middle';
  cans.fillText(str, can.width / 20, can.height);

  let div = document.createElement('div');
  div.id = id;
  div.style.pointerEvents = 'none';
  div.style.top = '0px';
  div.style.left = '0px';
  div.style.position = 'fixed';
  div.style.zIndex = '99999';
  div.style.width = document.documentElement.clientWidth + 'px';
  div.style.height = document.documentElement.clientHeight + 'px';
  div.style.background = 'url(' + can.toDataURL('image/png') + ') left top repeat';
  document.body.appendChild(div);
  return id;
};

// 设置系统水印
watermark.set = (str) => {
  let id = setWatermark(str);
  if (!id) return;

  setInterval(() => {
    if (document.getElementById(id || '') === null) {
      id = setWatermark(str);
    }
  }, 2000);
  window.onresize = () => {
    setWatermark(str);
  };
};

// 导出 watermark
export default watermark;

```

2.在main.js中导入watermark.js并配置成全局属性

```js
import Vue from 'vue';
import watermark from '@/utils/watermark';

Vue.prototype.$watermark = watermark;

```

3.在页面的js中调用

```js
export default {
  name: 'Header',
  data() {
    return {
        ... ...
    }
  },
  methods: {
      // 设置系统页面水印
      setWatermark() {
          this.$watermark.set(`test_watermark_msg`);
      }
  },
}

```