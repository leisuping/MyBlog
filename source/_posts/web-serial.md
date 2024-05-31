---
title : 前端页面与串口通讯
date: 2024-05-31 14:00:00
img: https://s21.ax1x.com/2024/05/31/pk8QzmF.jpg
tags:
 - js
 - 前端
categories: 
 - js
 - 前端
keywords:
 - 前端页面与串口通讯
 - 串口
---

## 关于Web Serial API
> 该API是JS本身navigator对象里的
> 使用该API需要服务器使用 https 协议，本地开发可以使用http://localhost:端口号
> 推荐[对串行端口执行读写操作](https://developer.chrome.com/docs/capabilities/serial?hl=zh-cn)
> 串口调试工具：[UartAssist](https://pan.baidu.com/s/1POnf_cbsa3oOBDl1dkd7HQ?pwd=kvq9)  提取码: kvq9

## 通过 Web Serial API 实现简单的串口通讯
### 特征检测（浏览器是否支持）
```js
// 判断浏览器支持串口通信
if ("serial" in navigator) {
    console.log("当前浏览器支持",true);
} else {
    console.log("当前浏览器不支持",false);
}
```

### 连接串口
```js
const port = ref("");
const ports = ref([]);
const reader = ref("");

// 连接串口
const connectToSerialPort = async () => {
  try {
    // 提示用户选择一个串口
    port.value = await navigator.serial.requestPort();
    // 获取串口信息
    let info = await port.value.getInfo()
    console.log('获取串口信息',info)
    // 获取用户之前授予该网站访问权限的所有串口。
    ports.value = await navigator.serial.getPorts();
    // 等待串口打开(可设置参数波特率)
    await port.value.open({ baudRate: 14400 });

    console.log(typeof port.value);
    ElMessage({
      message: "成功连接串口",
      type: "success",
    });
    readData();
  } catch (error) {
    // 处理连接串口出错的情况
    console.log("Error connecting to serial port:", error);
  }
};

```

### 发送数据
```js
const inputData = ref("");// 获取页面上需要发送的数据信息
const sendData = async () => {
  console.log("串口状态：",port.value,port.value.isOpen)
  // if (port.value && port.value.isOpen) {
  if (port.value) {
    if (inputData.value) {
      const writer = port.value.writable.getWriter();
      let sendValue = new TextEncoder().encode(inputData.value)
      console.log("发送数据:",sendValue);
      await writer.write(sendValue);
      await writer.close();
    } else {
      return ElMessage({
        message: "输入需要发送的数据内容",
        type: "warning",
        showClose: true,
        grouping: true,
        duration: 2000,
      });
    }
  } else {
    ElMessage({
      message: "串口未连接或未打开！",
      type: "warning",
      showClose: true,
      grouping: true,
      duration: 2000,
    });
    // console.error("串口未连接或未打开！");
  }
};

```


### 读取串口数据
```js
const readData = async () => {
  reader.value = port.value.readable.getReader();
  // 监听来自串口的数据
  while (true) {
    const { value, done } = await reader.value.read();
    if (done) {
      // 允许稍后关闭串口
      reader.value.releaseLock();
      break;
    }
    // 获取发送过来的数据
    // value 是 Uint8Array 数据类型
    console.log('Uint8Array',value);
    var str = Uint8ArrayToString(value)
    console.log('字符串：',str);
  }
};

```

> 注意：收发中文数据时可能会出现乱码，这个时候需要在 “串口调试助手” 工具的左上角，设置一下编码类型，默认为 ASCII 改为 UTF8 即可！


### 关闭串口
```js
const readData = async () => {
    await port.value.close();
    port.value = "";
    console.log("断开串口连接");
    ElMessage({
        message: "已成功断开串口连接",
        type: "success",
    });
}
```

![](./1.png)
![](./2.png)

推荐文章：[Web Serial API，在web端通过串口与硬件通信](https://blog.csdn.net/weixin_43155762/article/details/116888996)