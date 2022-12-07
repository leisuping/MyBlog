---
title : js日期操作工具类
date: 2022-12-05 19:04:20
img: https://s1.ax1x.com/2022/12/07/zc0Z8S.jpg
tags:
 - js
 - 前端
categories: 
 - 前端
 - js
keywords:
 - js
 - js日期操作工具类
---
## 一、说明
> 🎯 在js中会有许多关于日期的操作，比如日期的加减、格式化等等
> 本文收集了一些常用的js时间操作，有些是自己写的有些来源于网络上的一些copy
> 内容较多，有些也不记得是从哪里找的，就没一一粘贴出处了还请见谅！

## 二、工具类中一些实用方法

1. 获取当前时间：年月日时分秒
```js
// 获取当前时间年份
new Date().getFullYear()
// 获取当前时间月份（月份是从0开始的）
new Date().getMonth()+1
// 获取当前时间日期（获取的结果为整数，小于10的不会在数字前面自动填充0）
new Date().getDate()
// 获取当前时间小时（24小时）
new Date().getHours()
// 获取当前时间分钟
new Date().getMinutes()
// 获取当前时间秒钟
new Date().getSeconds()
// 获取当前时间毫秒数
new Date().getMilliseconds()
```

2. 获取当前时间 '年月日' 时间戳
```js
Date.parse(new Date(new Date().getFullYear() +'-'+ (new Date().getMonth()+1) + '-' + new Date().getDate()+' 00:00:00'))

```


3. 年月日 日期格式化
```js
/**
 * 通用日期格式化
 * @author ckk
 * @param date
 * @param fmt 如："yyyyMMdd"、"yyyy/MM/dd"、"yyyy-MM-dd hh:mm:ss"、"yyyy年MM月dd日"、"yyyy-MM-dd"等等
 * @returns {*}
 */
function dateFormat(date, fmt) {
    var obj = {
        "M+": date.getMonth() + 1,                      // 月
        "d+": date.getDate(),                           // 日
        "h+": date.getHours(),                          // 时
        "m+": date.getMinutes(),                        // 分
        "s+": date.getSeconds(),                        // 秒
        "q+": Math.floor((date.getMonth() + 3) / 3), // 季度
        "S": date.getMilliseconds()                     // 毫秒
    };
    if (/(y+)/.test(fmt)) {
        fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
    }
    for (var i in obj) {
        if (new RegExp("(" + i + ")").test(fmt)) {
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (obj[i]) : (("00" + obj[i]).substr(("" + obj[i]).length)));
        }
    }
    return fmt;
}

// 调用 dateFormat(new Date('2022/12/05'),'yyyyMMdd')
```

4. 年月日 日期格式化
```js
/**
 * 通用日期加减
 * @author ckk
 * @param dateStr
 * @param type 加减数的类型，d是天、h是小时...
 * @param num 正数加，负数减。ps：支持非整数
 * @returns {Date}
 */
function addDay(dateStr, type, num) {
    var m = 86400000;// 默认是天
    if(type == 'h'){
        m = m / 24;
    }else if(type == 'm'){
        m = m / 24 / 60;
    }else if(type == 's'){
        m = m / 24 / 60 / 60;
    }else if(type == 'ms'){
        m = m / 24 / 60 / 60 / 1000;
    }else{
        // type == 'd'
        // m = m;
    }
    return new Date(Date.parse(dateStr) + (m * num));
}

// 调用 addDay('2022-12-05','d',2)
```

5. js获取今天~n天后的时间日期数组
```js
function getWeekDay(n) {
	let today = new Date();
	let dateArr = []
	for (let i = 0; i < n; i++) {
		let newDate = new Date(today.getTime() + i * 1000 * 60 * 60 * 24)
		let year = newDate.getFullYear()
		let month = (parseInt(newDate.getMonth()) + 1) > 9 ? (parseInt(newDate.getMonth()) + 1) : "0" + (parseInt(
			newDate.getMonth()) + 1)
		let day = (newDate.getDate()) > 9 ? newDate.getDate() : "0" + newDate.getDate()
		let fullDate = `${year}-${month}-${day}`
		dateArr.push(fullDate)
	}
	return dateArr
}
```



