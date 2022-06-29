---
title: 如何优雅的处理前端一些小问题（一）
date: 2021-12-17 14:42:00
img: https://s4.ax1x.com/2021/12/17/TFqoy8.jpg
tags:
 - 前端
 - prototype
 - constructor
categories: 
 - 前端
 - prototype
keywords:
 - prototype
 - constructor
 - 前端笔试
---

## 一、故事分享
> 🎯 之前有分享过一些理论知识[卑微小前端每日一个“离职”小技巧](https://leisuping.github.io/MyBlog/client-tips/)今天我们来聊一些硬核一点的技术点
> 📝小编也是初级入门前端如有理解不当请各位大佬海涵哦！😯


## 二、进入正题
### 1. new 的实现

前提：了解js原型与原型链，可阅读[js基础篇之“深入浅出”](https://leisuping.github.io/MyBlog/facial-meridian-two/)

```js
/**
 * 1.获取构造函数
 * 2.创建新的对象
 * 3.将新对象的_proto_指向构造函数的prototype
 * 4.执行构造函数（为新对象添加属性跟方法）将构造中的this指向新对象
 * 5.判断函数的返回类型，如果是值类型，返回创建的对象。如果是引用类型，返回这个引用类型的对象。
 * @returns
 */
function myNew() {
  let Constructor = Array.prototype.shift.call(arguments);
  let obj = {};
  obj._proto_ = Constructor.prototype;
  var result = Constructor.apply(obj, arguments);
  return typeof result === 'object' ? result : obj;
}
```

### 2. call 的实现

前提：了解js的this指向，可阅读[js基础篇之“深入浅出”](https://leisuping.github.io/MyBlog/facial-meridian-two/)

```js
/**
 * 1.获取上下文
 * 2.重置上下文
 * 3.截取参数
 * 4.执行函数
 * 5.删除属性避免污染
 * 6.返回结果
 * @param {*} context 
 * @returns 
 */
Function.prototype.myCall = function (context) {
  context = context ? Object(context) : window;
  context.fn = this;
  let args = [...arguments].slice(1);
  let result = context.fn(...args);
  delete context.fn;
  return result;
}
```

### 3. 对象的深浅拷贝
> 关于对象的深浅拷贝
> 浅拷贝：
> 1.Object.assign(target,source)
> 2.es6对象扩展运算符
> 深拷贝：递归调用/序列化

```js
/**
 * 递归拷贝(简单版)
 * 1.判断obj是否为一个对象
 * 2.创建一个新对象，赋值为空
 * 3.循环obj对象，获取对象自身的属性值，并赋值给新对象（如果对象的属性值为'object'进入新一轮的拷贝）
 * 4.返回新对象
 * @param obj 
 * @returns 
 */
function deepClone(obj) {
  if (!obj || typeof obj != 'object') return;
  let newObj = Array.isArray(obj) ? [] : {};
  for (let item in obj) {
    if (obj.hasOwnProperty(item)) { // 判断对象是否包含自身属性（非继承）
      newObj[item] = typeof obj[item] === 'object' ? deepClone(obj[item]) : obj[item];
    }
  }
  return newObj;
}

// 序列化：JSON.stringify()序列化转为字符串，通过JSON.parse()再将其转为对象，最终得到的对象即为深拷贝的对象
//（简单数据结构的对象可以通过此方法实现深拷贝）
JSON.parse(JSON.stringify(obj))
```

递归拷贝(复杂版)[来源于网络 原文链接](https://juejin.cn/post/6946022649768181774)
```js
const isObject = (target) => (typeof target === "object" || typeof target === "function") && target !== null;

function deepClone(target, map = new WeakMap()) {
  if (map.get(target)) {
    return target;
  }
  // 获取当前值的构造函数：获取它的类型
  let constructor = target.constructor;
  // 检测当前对象target是否与正则、日期格式对象匹配
  if (/^(RegExp|Date)$/i.test(constructor.name)) {
      // 创建一个新的特殊对象(正则类/日期类)的实例
      return new constructor(target);  
  }
  if (isObject(target)) {
    map.set(target, true);  // 为循环引用的对象做标记
    const cloneTarget = Array.isArray(target) ? [] : {};
    for (let prop in target) {
        if (target.hasOwnProperty(prop)) {
            cloneTarget[prop] = deepClone(target[prop], map);
        }
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

### 4. 数组去重、数组扁平化、一维数组转二维数组

```js
// 补充：普通一维数组可以通过Set来实现去重
function unique (arr) {
  return Array.from(new Set(arr)); // 或者 [...new Set(arr)];
}
// 数组去重以及扁平化处理：
// 方法一（普通递归 + concat：因为方法返回的是数组，所以要用concat拼接）
function flatten(arr) {
  let result = [];
  for (let i = 0; i < arr.length; i++) {
    if (Array.isArray(arr[i])) {
      result = result.concat(flatten(arr[i]));
    } else {
      result.push(arr[i]);
    }
  }
  return result;
}
// 方法二（reduce + concat）
function flatten1(arr) {
  return arr.reduce(function (prev, next) {
    return prev.concat(Array.isArray(next) ? flatten1(next) : next);
  }, []);
}
// 一维数组转二维数组
/**
 * oneArray： 要分割的一维数组
 * num： 几个为一个数组的数量
 */
function oneTransTwo(oneArray, num) {
  return oneArray.reduce((v, item, index) => {
    let r = null;
    if (index % num === 0) {
      r = [...v, [item]];
    } else {
      v[v.length - 1].push(item);
      r = v;
    }
    return r;
  }, []);
};
```

### 5. 获取数组中最大值

```js
/**
 * 方法一
 * 利用es6新增的扩展运算符Math.max()方法获取最大值
 * @param {*} arr 
 * @returns 
 */
function getMax2(arr) {
  return Math.max(...arr);
}
/**
 * 方法二
 * 使用数组的sort排序方法
 * @param {*} arr 
 * @returns 
 */
function getMax1(arr) {
  return arr.sort(function (a, b) {
    return a - b;
  })[arr.length - 1];
}
/**
 * 方法三
 * 定义一个最大值max（预设数组中的一个值）
 * 遍历数组，与变量max比较，max小则将较大的值赋值给max
 * 最终返回max
 * @param {*} arr 数组
 * @returns max返回数组中的最大值
 */
function getMax(arr) {
  let max = arr[0];
  for (let i = 0; i < arr.length; i++) {
    if (max < arr[i]) {
      max = arr[i];
    }
  }
  return max;
}
```

### 6. 快速排序与冒泡排序

```js
/**
 * 快速排序：（不稳定）
 * 原理：首先是从数组中找出最小的元素放在最前面，然后从剩下的元素中挑出最小的排在第一次的最小值后面，以此类推，直到排序完毕。
 * 时间复杂度：最好的情况（n）；最差的情况（n*n）
 * 
 * 冒泡排序：（稳定）
 * 原理：依次比较相邻的两个值，如果后面的值比前面的值小，则将比较小的元素排在前面，
 * 时间复杂度：最好的情况（n*n）；最差的情况（n*n）
 * 
 * 时间复杂度：算法执行所需要的耗时
 * 空间复杂度：算法执行所需的内存大小
 * 稳定：如果a=b，a在b的前面，排序后a还是在b的前面
 * 不稳定：如果a=b，a在b的前面，排序后可能会交换位置
 */
// 快速排序
function quickSort(arr) {
  if (arr.length <= 1) return arr;

  var pivotIndex = Math.floor(arr.length / 2);
  var pivot = arr.splice(pivotIndex, 1)[0];
  var left = [];
  var right = [];
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < pivot) {
      left.push(arr[i])
    } else {
      right.push(arr[i])
    }
  }
  return quickSort(left).concat([pivot], quickSort(right))
}
// 冒泡排序
function sort(arr) {
  var temp = null;
  for (var i = 0; i < arr.length - 1; i++) {
    for (var j = i + 1; j < arr.length; j++) {
      if (arr[i] > arr[j]) {
        temp = arr[j];
        arr[j] = arr[i];
        arr[i] = temp;
      }
    }
  }
  return arr
}
// quickSort(arr);
// sort(arr)
```

<img src="https://s4.ax1x.com/2021/12/17/TFqTOS.jpg" width="98%"/>

> 最近在看一部动漫《国王排名》被波吉的毅力与勇气感动到，当然还有卡克的善良与忠诚 ...
> 希望我们也能有永恒的毅力与勇气面对未来！加油💪