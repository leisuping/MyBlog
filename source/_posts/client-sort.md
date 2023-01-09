---
title : SortableJS拖拽排序
date: 2023-01-09 19:46:26
img: https://s2.232232.xyz/static/384/2022/12/08-6391848427d48.jpeg
tags:
 - js
 - 前端
categories: 
 - js
 - 前端
keywords:
 - js
 - SortableJS拖拽排序
---


> 近期使用SortableJS插件实现了前端页面table行拖拽排序，在这里📝一下
> [SortableJS官方文档](http://www.sortablejs.com/index.html)

## 一、引入SortableJS
> 引入SortableJS可以有以下几种方式：
```js
// npm
npm i sortablejs

// yarn
yarn add -D sortablejs 

// CDN引入
<script src="https://cdn.jsdelivr.net/npm/sortablejs@latest/Sortable.min.js"></script>
```

## 二、使用SortableJS
> 页面：正常使用table组件
```html
<!-- 正常使用table组件即可 -->
<el-table :data="dataList" highlight-current-row v-loading="loading"  border>
  <el-table-column prop="No" :label="$t('index.number')" width="100"></el-table-column>
  <el-table-column prop="Room" :label="$t('index.group')"></el-table-column>
  <el-table-column prop="Msg1" :label="$t('list.Info1')"></el-table-column>
  <el-table-column prop="Msg2" :label="$t('list.Info2')"></el-table-column>
  <el-table-column prop="Msg3" :label="$t('list.Info3')"></el-table-column>
</el-table>
```

> js部分：
> 1、引入Sortable 2、在method中定义初始化Sortable函数 3、在mounted生命周期中调用初始化Sortable函数
> [SortableJS配置项](http://www.sortablejs.com/options.html)

```js
// 1、在对应的页面引入Sortable
import Sortable from 'sortablejs'

// 2、在method中定义初始化Sortable函数
initSortTable() {
  // 获取table对象（这里可以根据自己的页面上table组件情况来获取）
  const el = document.querySelectorAll('.el-table__body-wrapper > table > tbody')[0]
  // const sortable = new Sortable(el, options);
  // 根据具体需求配置options配置项
  this.sortable = new Sortable(el, {
    group: "name",
    sort: true,  // boolean 定义是否列表单元是否可以在列表容器内进行拖拽排序
    animation: 150,  // ms, number 单位：ms，定义排序动画的时间

    onEnd: (evt) => { // 监听拖动结束事件
      console.log(this) // this是当前vue上下文
      console.log(111,evt.oldIndex) // 当前行的被拖拽前的顺序
      console.log(222,evt.newIndex) // 当前行的被拖拽后的顺序

      const currRow = this.dataList[evt.oldIndex];
      this.dataList[evt.oldIndex] = this.dataList[evt.newIndex];
      this.dataList[evt.newIndex] = currRow;
      // 将排序结果更新到数据库中，根据自己项目的逻辑进行实现即可。
      console.log('排序成功，将结果保存到数据库中。。。。。',this.dataList)
    }
  })
},
mounted () {
  this.initSortTable()//页面组件渲染完毕初始化Sortable
},
```

效果图如下：
<img src="./sortable.jpg" width="100%"/>
