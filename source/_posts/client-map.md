---
title : js数组map()方法是否会改变原数组？
date: 2021-12-22 21:00:00
img: https://s1.ax1x.com/2022/12/07/zcDN7R.jpg
tags:
 - js
 - 前端
categories: 
 - js
 - 前端
keywords:
 - js数组map()方法是否会改变原数组
 - map
---
## JavaScript Array map() 方法

### 定义和用法
> map() 方法使用为每个数组元素调用函数的结果创建新数组。
> map() 方法按顺序为数组中的每个元素调用一次提供的函数。
> 注释：map() 对没有值的数组元素不执行函数。
> 注释：map() 不会改变原始数组。
> 摘自[w3school 文档](https://www.w3school.com.cn/jsref/jsref_map.asp)

### 测试
#### 基础数据类型
```js
const numbers = [65, 44, 12, 4];
numbers.map(item => {
	item += 10;
});
console.log(numbers); // 输出结果： [65, 44, 12, 4] // map()方法没有改变原数组
```

#### 引用数据类型
```js
const friends = [
	{
    	name: '楠竹',
        sex: 'girl',
        url: 'https://leisuping.github.io/MyBlog/'
    },
    {
    	name: 'Alisa',
        sex: 'girl',
        url: 'https://leisuping.github.io/MyBlog/'
    },
    {
    	name: 'jason',
        sex: 'girl',
        url: 'https://leisuping.github.io/MyBlog/'
    },
];
friends.map(item => {
	if (item.name === '楠竹') {
    	item.name = '楠竹菇凉';
    }
    if (item.name === 'jason') {
    	item.sex = 'boy';
    }
});
console.log(friends); // 结果如下 // map()方法改变了原数组
```
![](./map02.jpg)

基本数据类型的值存放在栈内存（Stack）里，是按值访问的。而引用类型的值保存在堆内存（Heap）中的，是按引用访问的。详情参见🔎[JavaScript 深入了解基本类型和引用类型的值](https://www.runoob.com/w3cnote/javascript-basic-types-and-reference-types.html)

### 总结
综上所述，数组的map()方法是否会改变原数组取决于原数组中的值。
如果数组中是基础数据类型，map()方法不会改变原数组
如果数组中是<span style="color: red">引用数据类型</span>，map()方法则<span style="color: red">会改变原数组</span>