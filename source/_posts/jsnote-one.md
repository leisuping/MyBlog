---
title : js 日期格式转换
date: 2024-02-23 19:44:26
img: https://s21.ax1x.com/2024/05/16/pknW29O.jpg
tags:
 - js
 - 前端
categories: 
 - js
 - 前端
keywords:
 - js
 - Date
---

## 1、js 获取当前日期前后几分钟（+n/-n分钟）
```js
// 日期格式：'YYYY-MM-DD HH:mm:ss'
const nowTime = (minut) => {
  var date=new Date();     //1. js获取当前时间
  var min=date.getMinutes();  //2. 获取当前分钟
  date.setMinutes(min+ (minut ? minut : 1));  //3. 设置当前时间+10分钟：把当前分钟数+10后的值重新设置为date对象的分钟数
  var y = date.getFullYear();
  var m = (date.getMonth() + 1) < 10 ? ("0" + (date.getMonth() + 1)) : (date.getMonth() + 1);
  var d = date.getDate() < 10 ? ("0" + date.getDate()) : date.getDate();
  var h = date.getHours() < 10 ? ('0' + date.getHours()) : date.getHours()
  var f = date.getMinutes() < 10 ? ('0' + date.getMinutes()) : date.getMinutes()
  var s = date.getSeconds() < 10 ? ('0' + date.getseconds()) : date.getSeconds()
  var formatdate = y+'-'+m+'-'+d + " " + h + ":" + f + ":" + s;
  return formatdate
}
console.log(nowTime(10)); // 获取10分钟后的日期时间格式：'YYYY-MM-DD HH:mm:ss'

```


## 2、js 将日期时间转换为“刚刚”、“几分钟前”、“几天前”等
> date为传入时间 例："2023/5/10 11:32:01" 2023-02-01 09:32:01
> type为返回类型 例：- / 年月日 年
> 默认规则，大于等于一天(24小时)展示'X天前；大于等于30天且小于365天展示'X个月前'；大于等于365天且展示'x年前'，否则按照传入参数规则展示
> zeroFillFlag参数为是否数字补零，默认补零
> 时间参数date为必写参数 [原文链接](https://juejin.cn/post/7317704519160266779)

时间处理公式依据：
● 10秒：  10 * 1000
● 1分钟： 60 * 1000
● 1小时： 60 * 60 * 1000
● 24小时（一天）：60 * 60 * 24 * 1000


```js
const formatPast = (date, type = "default", zeroFillFlag = true) => {
  // 定义countTime变量，用于存储计算后的数据
  let countTime;
  // 获取当前时间戳
  let time = new Date().getTime();
  // 转换传入参数为时间戳
  let afferentTime = new Date(date).getTime();
  // 当前时间戳 - 传入时间戳
  time = Number.parseInt(`${time - afferentTime}`);
  if (time < 10000) {
    // 10秒内
    return "刚刚";
  } else if (time < 60000) {
    // 超过10秒少于1分钟内
    countTime = Math.floor(time / 1000);
    return `${countTime}秒前`;
  } else if (time < 3600000) {
    // 超过1分钟少于1小时
    countTime = Math.floor(time / 60000);
    return `${countTime}分钟前`;
  } else if (time < 86400000) {
    // 超过1小时少于24小时
    countTime = Math.floor(time / 3600000);
    return `${countTime}小时前`;
  } else if (time >= 86400000 && type == "default") {
    // 超过二十四小时（一天）且格式参数为默认"default"
    countTime = Math.floor(time / 86400000);
    //大于等于365天
    if (countTime >= 365) {
        return `${Math.floor(countTime / 365)}年前`;
    }
    //大于等于30天
    if (countTime >= 30) {
        return `${Math.floor(countTime / 30)}个月前`;
    }
    return `${countTime}天前`;
  } else {
    // 一天（24小时）以上且格式不为"default"则按传入格式参数显示不同格式
    // 数字补零
    let Y = new Date(date).getFullYear();
    let M = new Date(date).getMonth() + 1;
    let zeroFillM = M > 9 ? M : "0" + M;
    let D = new Date(date).getDate();
    let zeroFillD = D > 9 ? D : "0" + D;
    // 传入格式为"-" "/" "."
    if (type == "-" || type == "/" || type == ".") {
        return zeroFillFlag
            ? Y + type + zeroFillM + type + zeroFillD
            : Y + type + M + type + D;
    }
    // 传入格式为"年月日"
    if (type == "年月日") {
        return zeroFillFlag
            ? Y + type[0] + zeroFillM + type[1] + zeroFillD + type[2]
            : Y + type[0] + M + type[1] + D + type[2];
    }
    // 传入格式为"月日"
    if (type == "月日") {
        return zeroFillFlag
            ? zeroFillM + type[0] + zeroFillD + type[1]
            : M + type[0] + D + type[1]
    }
    // 传入格式为"年"
    if (type == "年") {
        return Y + type
    }
  }
};
console.log(formatPast("2024-1-1 11:11:11")); // 3天前
console.log(formatPast("2023-11-1 11:11:11")); // 2个月前
console.log(formatPast("2015-07-10 21:32:01")); // 8年前
console.log(formatPast("2023-02-01 09:32:01", "-", false)); // 2023-2-1
console.log(formatPast("2023.12.8 19:32:01", "/")); // 2023/12/08
console.log(formatPast("2023.12.8 19:32:01", ".")); // 2023.12.08
console.log(formatPast("2023/5/10 11:32:01", "年月日")); // 2023年05月10日
console.log(formatPast("2023/6/25 11:32:01", "月日", false)); // 6月25日
console.log(formatPast("2023/8/08 11:32:01", "年")); // 2023年
```