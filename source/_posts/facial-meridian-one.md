---
title : 一定要懂的js基础，你会了吗？
date: 2021-12-07 17:03:20
img: https://s2.232232.xyz/static/384/2022/12/08-639182ccac162.jpeg
tags:
 - 前端
 - 事件循环
categories: 
 - 前端
 - js
keywords:
 - 事件循环、异步解决方案
 - 一定要懂的js基础，你会了吗？
---
## 一、EventLoop事件循环
> 🎯 js是一个单线程脚本语言。
> 在执行比较耗时的任务时很容易导致线程阻塞，导致页面卡顿的情况。为了解决这个问题就有了 eventLoop 事件循环。
> eventLoop 将js线程中的任务分为：宏任务、微任务。
> 在任务进入执行栈后，会先判断是否为宏任务，宏任务进入主线程中执行，微任务进入微任务队列等待执行。
> 宏任务执行结束（就已经确保宏任务栈空了）再判断微任务栈是否有微任务需要执行。

![](./stack.png)

### 1.宏任务macro-task、微任务micro-task
1. 宏任务：js整体代码、setTimeout、setInterval ... ...
2. 微任务：promise ... ...（回调、需要耗时的）
补充：虽然 setTimeout 是宏任务但是它的回调会被放入微任务队列中。
虽然 promise 是微任务，但是new Promise会立即执行，回调会放入微任务栈中。

![](./task.png)
![](./evenloop.png)


## 二、Promise异步解决方案
> 🎯 Promise是js中的一种异步解决方案。
> 支持链式调用 .then() .catch() .finally()，解决了传统异步解决方案（ajax）回调地狱的问题。（缺点：所有的错误/异常需要在回调函数catch中捕获处理）
> promise函数自带两个参数resolve、reject，异步请求成功执行resolve，失败执行reject。

### 1.promise三种状态（状态发生变化后不可逆）
● Pending 初始状态，进行中
● Resolved（fulfilled） 已完成，成功
● Rejected 已失败

### 2.promise链
promise的每一个链式调用都会返回一个新的promise对象，又可以接着使用.then() .catch() 由此形成promise链。在 then 中使用了return，那么return的值会被promise.resolve() 包装。

![](./promise1.png)

### 3.promise.all()与promise.race()
1. promise.all() 并发执行多个任务，所有任务都执行完成才进入回调。
2. promise.race() 只要有一个任务完成，就执行回调得到结果。
补充：
then() 得到异步成功的信息，返回promise对象可以继续调用下一个then()
catch() 得到异步失败的信息，返回promise对象可以继续调用下一个then()
finally() 无论成功失败都执行，返回promise对象可以继续调用下一个then()


补充：关于宏任务、微任务执行顺序
![](./promise.png)

执行顺序
1、js代码、new Promise内容（promise构造内的代码是立即执行的）
2、new Promise 回调（then函数在promise.resolve()后执行）
3、setTimeout 回调
4、new Promise 里面的setTimeout 回调

new Promise 会立即执行，then()、catch()、finally()等回调会被放在微任务队列中后执行。（会先于setTimeout的回调）


## 三、Async、Await
> 🎯 async/await 也是js中异步的解决方案，更进一步优化了异步代码块。
> 其原理就是generator 加上了promise语法糖，内部实现了自动执行generator。

### 1.Async、Await的特点
async返回的是一个promise对象，而await就是等promise的结果返回后再执行。（await接收的就是promise返回的结果，后面不一定要使用.then）
async 标记当前代码块为异步，await则是将异步变为同步（.then的语法糖，返回promise对象）

await只能在async函数中，不能单独使用。

优点：
1、简洁明了
2、可以结合promise进行异步的处理

```js
// Tip1
async function test() {
    const { data } = await axios.post('api/v3/batchadd', objarray);
    const { data2 } = await axios.post('api/v3/batchadd2', objarray2);
    // 这里也可以做一些别的处理
    return data.concat(data2);
}
test().then(res => {
// 其他的逻辑处理
}).catch((error) => {
// 处理异常
});

// Tip2
// 也可以在async中使用try catch来处理异常
async function test() {
    try {
        const { data } = await axios.post('api/v3/batchadd', objarray);
        const { data2 } = await axios.post('api/v3/batchadd2', objarray2);
        // 这里也可以做一些别的处理
        return data.concat(data2);
    }catch(err) {
    // 处理异常
    }
}

// 也可以用promise.race(test1, test2);实现。（并发执行可以使用promise.all() 来实现）
```



