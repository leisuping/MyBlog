---
title : JS进制转换
date: 2023-12-20 20:04:06
img: https://s11.ax1x.com/2024/02/29/pFwD8b9.jpg
tags:
 - js
 - 前端
categories: 
 - 前端
 - js
keywords:
 - js
 - JS进制转换
---
## 1、ArrayBuffer转16进度字符串

```js
let ab2hex=(buffer) => {
  var hexArr = Array.prototype.map.call(
    new Uint8Array(buffer),
    function (bit) {
      return ('00' + bit.toString(16)).slice(-2)
    }
  )
  return hexArr.join('');
}
```

```js
//根据key值得到数组的下标  arr:数组  key:比较key  val:比较内容    （-1 没有匹配到
let inArray=(arr, key, val)=> {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i][key] === val) {
      return i;
    }
  }
  return -1;
}
```

## 2、16进制字符串转换为字符串文本

```js
let hexToString = (hex)=> {
  var tmp = '';
  if (hex.length % 2 == 0 ) {
      for (var i = 0; i < hex.length; i += 2) {
          tmp += '%' + hex.charAt(i) + hex.charAt(i + 1);
      }
  }
  return decodeURIComponent(tmp);
}
```

## 3、字符串转16进制

```js
let stringToHex =(str)=> {
  var hex = "";
  for (var i = 0; i < str.length; i++) {
      hex += str.charCodeAt(i).toString(16);
  }
  return hex;
}
```

## 4、int转byte

```js
let intToByte=(i)=> {
  var b = i & 0xFF;
  var c = 0;
  if (b >= 128) {
      c = b % 128;
      c = -1 * (128 - c);
  } else {
      c = b;
  }
  return c
}
```

## 5、十六进制字符串转换为数组

```js
let hexStringToArray=(str='')=> {
  var pos = 0;
  var len = str.length;
  if (len % 2 != 0) {
    return null;
  }
  len /= 2;
  var arrBytes = new Array();
  for (var i = 0; i < len; i++) {
    var s = str.substr(pos, 2);
    arrBytes.push("0x"+s);
    pos += 2;
  }
  return arrBytes;
}
```

## 6、不足4位前面补0

```js
//+0不足4位,不足则在前面补充0。四位数 12 得到 0012、270得到0270、1234得到1234、 27023 得到 27023
let addPreZero=(num)=> {
  var t = (num + '').length,
    s = '';
  for (var i = 0; i < 4 - t; i++) {
    s += '0';
  }
  return s + num;
}

//+0不足8位,不足则在前面补充0
let addPreZero8=(num)=> {
  var t = (num + '').length,
    s = '';
  for (var i = 0; i < 8 - t; i++) {
    s += '0';
  }
  return s + num;
}
```
## 7、一维数组转二维数组

```js
/*一维数组转二维数组
source： 要分割的一维数组
num： 几个为一个数组的数量
 */
let oneTransTwo=(source, num) => {
  return source.reduce((v, item, index) => {
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

## 8、2进制(bit)转16进制(byte)

```js
/**
 * 2进制(bit)转16进制(byte)
 */
let binaryNumberToBhex=(binary)=>{
  return parseInt(binary,2).toString(16)
}
```

## 9、转16进制补0

```js
/**
 * 不足两位补0
 * @param {*} yn16 是否转16进制
 * @param {*} fq 1:前补0，后补0
 */
let to16add0=(num,yn16=false,fq=1)=>{
let str = yn16 ?num.toString(16):String(num)
if(str.length<2){
    
    return fq==1?'0'+str:str+'0';
}else{
    return str
}
}
```

## 10、数组转16进制字符串

```js
/* 数组转16进制字符串*/
let byteArrayToHex=(arrayValue)=> {
    var hexStr = "";
    for (var arrayItem of arrayValue) {
        var charHex = arrayItem.toString(16);
        charHex = (Math.floor(charHex.length % 2) == 0) ? charHex : `0${charHex}`;
        hexStr = `${hexStr}${charHex}`;
    }
    return hexStr.toLocaleUpperCase();
}
```
## 11、16进制转2进制

```js
/* 16进制转2进制  -byte转bit，不足前位+0*/
let hexStringToBinary=(hex)=>{
  return ("00000000" + (parseInt(hex, 16)).toString(2)).substr(-8);
}
```

## 12、位异或

```js
/* 位异或  yn16:是否转换得到16进制字符串*/
let toNor=(str,yn16=false) =>{
    str = String(str)
    let crc = '00';
    for (var i = 0, len = str.length; i < len; i += 2) {
        let code = parseInt(str.substr(i, 2), 16);
        crc = crc ^ code;
    }
    if(yn16){
        crc = crc.toString(16)
        if(crc.length<2){
        return '0'+crc;
        }else{
        return crc
        }
    }else{
        return crc;
    }
}
```

[待完善... ]
