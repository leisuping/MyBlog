---
title: Vue使用eventBus总线，实现非父子组件间的通信
date: 2021-11-30
img: https://s6.jpg.cm/2021/12/07/LQepfy.jpg
tags:
 - Vue
 - Bus
 - 组件通讯
categories: 
 - 前端
 - Vue
 - 组件通讯
keywords:
 - 组件通讯
 - Vue使用eventBus总线，实现非父子组件间的通信
---

## 一、前提
vue提供的数据间通讯方式有很多种，比较常用的有：
1.父子组件使用props传值或者provide/inject
> 父组件中通过 this.$refs.子组件ref值.子组件方法；子组件中通过 this.$emit('父组件方法') 来调用父组件方法。
> 父组件中通过provide来提供变量, 然后再子组件中通过inject来注入变量。
> (只要调用了inject 就可以注入provide中的数据，不局限于只能从当前父组件的props属性中回去数据)

2.非父子组建常用的通讯方式有 Vuex 、eventBus或者localStorage、sessionStorage
> vuex是vue.js数据状态管理，集中式存储管理应用的所有组件的状态，通过vuex来实现数据的全局共享。（适用于比较庞大的系统，小项目不建议使用）
> eventBus事件总线，所有组件共用的一个事件中心。可以向该中心注册发送事件或接收事件， 所有组件都可以通知其他组件。（适用于较小的项目，项目过大难以维护）

> 通过window.localStorage.getItem(key)获取数据(JSON.parse() / JSON.stringify() 做数据格式转换)
> 通过window.localStorage.setItem(key,value)存储数据
> localStorage / sessionStorage可以结合vuex，实现数据的持久保存，解决数据和状态混乱问题。

## 二、eventBus事件总线

> eventBus实现vue跨级组件之间的通信，在项目中，如果数据和业务逻辑不是特别复杂，没有必要使用Vuex，用eventBus就能实现我们想要的功能而且代码比较简洁直观。
> 那么在vue项目中如何通过eventBus事件总线来实现非父子组件之间的数据通讯呢？
> 在简单的场景下，可以使用一个空的Vue实例作为中央事件总线

> 场景：这里模拟在右侧列表中操作数据后，局部刷新左侧菜单的统计
> 注：左侧菜单nav是一个单独的组件，右侧列表则是home中的一个视图（组件）[两个页面是非父子组件]

![](./table.jpg)

```js
// 在项目的main.js中全局定义，将bus挂载到vue.prototype上
import Vue from 'vue';

Vue.prototype.bus = new Vue();

```

```js
// 在列表页test.vue（A）中触发事件
export default {
  data() {
    ... ...
  },
  methods: {
    getData() {
      // 初始化页面
      ... ...
      // 更新左侧菜单
      this.bus.$emit('refreshMenu');
    },
  },
}

```


```js
// 在菜单组件nav.vue（B）中监听事件
export default {
  name: "Nav",
  data() {
    ... ...
  },
  mounted() {
    // 监听事件
    this.bus.$on('refreshMenu', () => {
      this.getData();
    });
  },
  beforeDestroy() {
    // 在组件销毁时卸载注册的总线事件，否则会多次挂载，造成触发一次但多个响应的情况
    this.bus.$off(['refreshMenu'])
  },
  methods: {
    getData() {
      // 初始化菜单页面（获取菜单统计数据）
      ... ...
    },
  },
}

```

