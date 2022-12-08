---
title : Element table组件上移下移数据实现自定义排序
date: 2021-11-11
img: https://s2.232232.xyz/static/384/2022/12/08-639184512ff4e.jpg
tags:
 - Vue
 - ElementUI
 - table
categories: 
 - 前端
 - Vue
 - ElementUI
keywords:
 - table
 - ElementUI
 - table组件上移下移数据实现自定义排序
---

<img src="./IMG_1987.JPG"/>

## 一、需求
> 在页面操作上移下移按钮，对表格行数据进行移动
> 实现表格自定义排序

## 二、处理方式
> 思路：
> 1.监听 table 数据行的选中事件（获取当前选中行的数据）假设当前行 currentData
> 2.上移、下移按钮点击事件：handleSortUp、handleSortDown
> 3.保存选中行的下标 currentIndex
> 4.保存选中行的上一行数据 currentData
> 5.移除选中行（下一行数据上移），并将上一行数据 currentData 插入到选中行的位置
### 1. 实现table数据单行选中，并获得该行数据的index信息（用于后面上移下移数据）

> 定义table并绑定数据
> 设置table的 current-change 事件（实现单行选中获取行信息）

```html
<el-table
  ref="sortTable"
  :data="sortTablData"
  :highlight-current-row="true"
  @current-change="handleSortTableCurrentChange">

  <el-table-column label="序号" width="50" align="center">
    <template slot-scope="scope">
      {{ scope.$index + 1 }}
    </template>
  </el-table-column>
  <el-table-column
    label="单位名称"
    align="center"
    prop="departmentName">
  </el-table-column>
</el-table>

```

> 在table的 current-change 回调中获取到当前选中行的index，并赋值给全局变量 currentIndex
> 在 handleSortUp 跟 handleSortDown 中实现上移下移

> 使用 splice 函数，上移实现方式：
> a. 记录当前选中行的index，然后保存当前选中数据的上一行数据 upData
> b. 然后再将 upData 移除[这样当前选中行就会自动上移到 upData 的位置]
> c. 将事先保存好的 upData 插入到当前选中行的index [ 这样就完美实现了选中行上移，以此类推 ]

```js
// 排列列表选中某行数据回调事件（获取当前选中行的下标）
handleSortTableCurrentChange(val) {
  this.currentIndex = this.sortTablData.findIndex(i => i === val);
},
//排序上移
handleSortUp() {
  let index = this.currentIndex;
  if (index > 0) {
    let upData = this.sortTablData[index - 1];
    this.sortTablData.splice(index - 1, 1);
    this.sortTablData.splice(index, 0, upData);
    this.currentIndex -= 1;
  }
},
//排序下移
handleSortDown() {
  let index = this.currentIndex;
  if (this.sortTablData && index < this.sortTablData.length) {
    let downData = this.sortTablData[index + 1];
    this.sortTablData.splice(index + 1, 1);
    this.sortTablData.splice(index, 0, downData);
    this.currentIndex += 1;
  }
},
```

### 2. 界面预览
![](./code-img.jpg)

