---
title : 动态加载系统文字
date: 2022-04-01
img: https://s6.jpg.cm/2021/12/07/LQe3WG.jpg
tags:
 - Vue
 - js
 - font
categories: 
 - 前端
 - Vue
 - js
keywords:
 - 前端
 - Vue
 - 动态加载系统文字
---

<img src="https://s6.jpg.cm/2021/12/07/LQe3WG.jpg" width="90%"/>

## 一、需求
> 🎯 系统中需要用到不同字体文件，且可以随时增加新的字体文件
> 为了方便拓展维护，将字体文件放在服务端，前端在系统初始化的时候去请求接口获取所有的字体文件...
> 详细步骤如下：
> 1、将字体文件上传到服务端（cdn加速）后续拓展只需要将新的字体文件上传至服务端即可
> 2、前端通过接口获取字体文件的url及名称等信息
> 3、在接口中用js动态生成style标签，并动态组装字体css字符串，将其添加到style标签中
> 4、在系统初始化后调用接口即可

## 二、实现
> 平时我们在系统中引入静态字体资源，新建css/scss文件，将本地静态资源引入css/scss中。在页面使用时 import 指定的字体css/scss文件即可

```css
@font-face {
       font-family: 'KaiTi'; /**重命名字体名**/
       src: url('./KaiTi.ttf'); /**本地资源相对路径**/
       font-weight: normal;
       font-style: normal;
}
@font-face {
       font-family: 'HeiTi';
       src: url('./HeiTi.ttf');
       font-weight: normal;
       font-style: normal;
}
@font-face {
       font-family: 'SongTi';
       src: url('./SongTi.ttf');
       font-weight: normal;
       font-style: normal;
}
@font-face {
       font-family: 'WeiTi';
       src: url('./WeiTi.ttf');
       font-weight: normal;
       font-style: normal;
}
```

> 动态引入字体文件则需要将以上操作用js动态生成


```js
// 引入 axios
import axios from 'axios';

// 字体数组：定义字体数组
export let fontfamilys = [];

// 请求接口获取字体信息
export function getFonts() {
  axios.get(process.env.API_HOST + 'api/getFonts').then(res => {
      if (res.code == 200) {
          // 获取字体信息 res.data.fonts
          // 创建style标签，并设置type
          const fontStyle = document.createElement('style');
          fontStyle.type = 'text/css';
          // 动态组装字体css字符串
          let fonts = res.data.fonts.map(m => {
              return `@font-face {font-family: '${m.font}'; src: url('${ flag ? m.oss : localServerUrl + m.path }');font-weight: normal;font-style: normal;}`
          })
          fontStyle.innerText = fonts.join(' ');
          // 将style标签追加到head标签中
          document.getElementsByTagName('head')[0].appendChild(fontStyle);
          // 组装字体类别数组
          fontfamilys = res.data.fonts.map(m => ({ name: m.name, value: m.font }));
          console.log('开始加载字体...');
      }
  });
}
```

```js
// 动态生成的字体类型数组如下：
let fontfamilys = [
  {
      name: '宋体',
      value:'SongTi'// 对应字体文件别名
  },
  {
      name: '楷体',
      value:'KaiTi'
  },
  {
      name: '黑体',
      value:'HeiTi'
  },
  {
      name: '魏体',
      value:'WeiTi'
  },
  {
      name: '标宋',
      value:'JianBiaoSong'
  }
];
```


```html
<!-- 在界面中可根据 fontfamilys 数组的 value 值切换（style样式中的font-family属性值）页面字体 -->
<div class="tempImg" @click="clickModel(item)">
  <span :style="
    `color:${item.fontColor};
    font-family:${item.fontCode};
    -webkit-text-stroke: ${item.strockOpacity}px ${item.strockColor};`">
    测试文字
  </span>
</div>
```
