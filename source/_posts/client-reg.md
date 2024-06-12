---
title : 前端之正则校验（一）
date: 2021-11-24
img: https://s2.232232.xyz/static/384/2022/12/08-639184480d1df.jpg
tags:
 - js
 - jquery
 - 正则
categories: 
 - 前端
 - 正则
keywords:
 - js
 - reg
 - 正则
---
## 一、正则表达式
> 🎯 归纳最近用到的一些正则表达式

```js
// 正则表达式
// 不包含特殊符号（长度0-50）
export const regSpecialSymbol = /^((?!\\|\/|:|\*|test|null|？|:|;|~|,|、|。|，|【】|{}|&|$|¥|=|\?|<|>|\||'|%).){1,50}$/
// 不含数字
export const regNotNum = /[^0-9]$/
// 不包含*或null或test
export const regNotStart = /[^*|null|test]$/
// 护照（包含香港、澳门）
export const regPassport = /(^[EeKkGgDdSsPpHh]\d{8}$)|(^(([Ee][a-fA-F])|([DdSsPp][Ee])|([Kk][Jj])|([Mm][Aa])|(1[45]))\d{7}$)/
// 港澳居民来往内地通行证
export const regGaoPass = /^[H|h|M|m](\d{8}|\d{10})$/
// 台湾居民来往大陆通行证
export const regTwPass = /^\d{8}|^[a-zA-Z0-9]{10}|^\d{18}$/
// 外国人永久居留身份证，如：USA110074110419、aUs110074110419
export const regForeignerResideCard = /^[a-zA-Z]{3}\d{12}$/
// 身份证
export const regIdCard = /(^\d{8}(0\d|10|11|12)([0-2]\d|30|31)\d{3}$)|(^\d{6}(18|19|20)\d{2}(0\d|10|11|12)([0-2]\d|30|31)\d{3}(\d|X|x)$)/
// 统一社会信用代码
export const regSocialCreditCode = /^([0-9A-HJ-NPQRTUWXY]{2}\d{6}[0-9A-HJ-NPQRTUWXY]{10}|[1-9]\d{14})$/
// 企业注册号
export const regCompanyIdNum = /^[A-Za-z0-9]\w{14}$/g
// 工商注册号
export const regGszcCode = /^[1-7]\d{14}$/
// 组织机构代码
export const regZzjgCode = /[0-9A-HJ-NPQRTUWXY]{9}/
// 税务登记号：为15位数字和大写英文字母
export const regUnitNum = /^[A-Z0-9]{15}$/
// 事业单位证书号（长度不能超过12个字符，不能含有除数字或字母之外的字符）
export const regSyzsCode = /^[A-Za-z0-9]{12}$/
// 社会组织登记证号（长度不能超过50个字符，不能含有除数字或字母之外的字符）
export const regShzzdjCode = /^[A-Za-z0-9]{50}$/
// 金额 (大于0或者正数，六位小数)【/(^[1-9]{1}[0-9]*$)|(^[0-9]*\.[0-9]{2}$)/】
export const regMoney =/^([1-9]\d{0,9}|0)(\.\d{1,6})?$/

// 手机号验证
export const regPhone = /^(1[3|5|4|6|7|8|9]\d{1}[*|\d]{4}\d{4})$/
// 邮箱验证
export const regEmail = /^[a-zA-Z0-9_]+[a-zA-Z0-9_\-\.]+[a-zA-Z0-9_]+@[\w-]+\.[\w-]+$|^[a-zA-Z0-9_]+[a-zA-Z0-9_\-\.]+[a-zA-Z0-9_]+@[\w-]+\.[\w-]+\.[\w-]+$/

// 英文字母首字母必须大写
export const capitalize_first_letter = /^(([A-Z0-9\u4e00-\u9fa5]+)([\w\u4e00-\u9fa5]+)?([\W]*))+$/
// 不能以空格、-、&符号开头、结尾
export const ignore_spaces = /^([\u4e00-\u9fa5A-Za-z0-9\(\)]+)(.*[\u4e00-\u9fa5A-Za-z0-9\(\)]+)?$/
export const ignore_spaces_1 = /^[^\s\-\.\,;:'"!@\#\$%\^\&\*\(\)\<\>\/\|\\].+$/


// input 只能输入框只能输入正整数，输入同时禁止了以0开始的数字输入，防止被转化为其他进制的数值。
// 不能输入零时
<input type='text' oninput="value=value.replace(/^(0+)|[^\d]+/g,'')" />

// 能输入零时
<input type='text' oninput="value=value.replace(/^0+(\d)|[^\d]+/g,'')" />

// 附：只能输入中文:
<input type="text" oninput="this.value=this.value.replace(/[^\u4e00-\u9fa5]/g,'')" />  
// 附：只能输入英文:
<input type="text" oninput="this.value=this.value.replace(/[^a-zA-Z]/g,'')" />  



// el-input 只能输入框只能输入正整数
<el-input size="small"
    οnkeyup="value=value.replace(/^(0+)|[^\d]+/g,'')"
    v-model="count"
    maxlength="9"></el-input>

data() {
   return {
      count: 0
   }
}

export const VersionReg = /^(V|v)([1-9]\d|[1-9])(.([1-9]\d|\d)){2}$/

// 指定范围随机数：
parseInt(Math.random() * 8)


```

## 二、小插件

> 推荐vs code中一个关于正则的小插件 any-rule 个人觉得还可以，虽然包含的正则数目不多，但是常规的一些都有。
> 在vs code应用商店中下载安装后，打开一个页面右键就会有：正则大全75条
> 当然，特殊的正则也是可以根据个人需要做一些调整的。

![](./reg1.jpg)



