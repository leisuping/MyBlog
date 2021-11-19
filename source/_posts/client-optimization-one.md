---
title : 前端页面优化之防抖节流
date: 2021-11-19
tags:
 - Vue
 - 防抖
 - 节流
categories: 
 - 前端
 - Vue
---
## 一、引题
 🎯 最近同事在做项目过程中遇到一个问题：一个单页面的数据保存，在用户疯狂点击保存按钮时，数据库会保存多条数据。对于这个问题除了防抖其实有很多解决办法，网上也有不少已经处理好的架构，但是我认为这个还是可以收录在博客里的哈哈哈方便自己巩固一下基础知识，也锻炼锻炼自己写博客的能力。


## 二、关于防抖节流
### 1. 什么是防抖

> 防抖与节流都是通过减少用户请求，优化前端性能、减少服务器压力

> 防抖：n秒执行1次，在n秒内被再次触发将会重新计时。（其他请求会被重置/清除）clearTimeout
> 简单理解为：事件响应函数在一段时间后才会执行

> 使用场景：
> a. search搜索联想
> b. input输入校验，onChange事件、onInput事件
> c. 用户不断输入时，防抖节约请求资源。
> d. window窗口不断改变大小时，防止重复渲染

### 2. 什么是节流

> 节流：间隔时间执行

> 使用场景：
> a. 鼠标高频点击，防止重复提交
> b. 滚动加载
> c. 搜索相关也可以使用


## 三、实现方式

> 在系统的utils文件夹下面创建一个common.js 将一些公共的函数放入
> js防抖（在指定的时间单位内只触发一次）与节流（在触发事件的一段时间内，不会再触发第二次）
> 这里我只是做了一个简易版，根据不同的业务可在此基础上进行拓展。


### 1. 防抖
```js
var timer = null; // 定义定时器

/**
 * 防抖
 * @param {*} fn 回调
 * @param {*} delay 延迟时间
 * @param {*} msg 提示信息
 */
 export function debance(fn, delay, msg) {
  delay = delay ? delay : 5000;
  if (timer) {
    clearTimeout(timer);
    if (msg) {
        // 这里可以添加提示信息，告诉用户不要频繁操作
        console.warn('提示信息：您已经操作过啦！请勿频繁操作哦！');
    }
  }
  timer = setTimeout(() => {
    fn(); // 执行函数，并且清空定时器
    timer = null;
  }, delay);
}
```

### 2. 节流
```js
var throttleLast = 0; // 设置时间戳变量

/**
 * 节流
 * @param {*} fn 回调
 * @param {*} delay 间隔时间
 * @param {*} msg 提示信息
 */
export function throttle(fn, delay, msg) {
  delay = delay ? delay : 2000;
  let current = Date.now(); // 获取当前时间毫秒数
  if (current > throttleLast + delay) {
    throttleLast = current;
    fn();
  } else {
      // 这里可以添加提示信息，告诉用户不要频繁操作
      console.warn('提示信息：您已经操作过啦！默认2秒钟内将会刷新，请勿频繁操作哦！');
  }
}
```

### 3. 在页面中使用

```js
// 引入函数（这里我就不详细描述了，具体的还是根据系统业务来）
import {
  debance,
  throttle,
} from "@/utils/common";

export default {
    data() {
        return {
            ... ...
        }
    },
    methods: {
        getList() {
            // 请求接口获取列表数据
        },
        // 页面初始化获取列表数据
        getData() {
            throttle(this.getList, 0, '');
        },
        saveData() {
            // 调用接口保存数据
        },
        // 保存操作按钮点击事件
        handleSave() {
            debance(this.saveData, 0, '');
        }
    }

}

```

## 四、结语
> 函数防抖和函数节流都是防止某一时间频繁触发，很多情况下，防抖与节流也都能够达到我们想要的效果，但是其具体的实现原理还是有区别的。
> 函数防抖是某一段时间内只执行一次，而函数节流是间隔时间执行
> 所以在使用时只需要区分不同场景便可灵活操作，当然两者也是可以结合使用的哦
