---
title : 微信小程序上传文件
date: 2022-07-12 15:35:34
img: https://s6.jpg.cm/2021/12/07/LQeHZL.jpg
tags:
 - 微信小程序
 - upload
categories: 
 - 前端
 - 微信小程序
keywords:
 - weixin
 - 微信小程序上传文件
---
## 一、引题
> 🎯 前言：这次的需求是要在小程序里下载模板文件，然后编辑好数据再上传
> 这里就涉及到小程序的文件下载、保存、上传等功能。通常我们在小程序中处理图片文件的上传下载还是很ok的，但是如果是pdf Excel等文件就会稍复杂一些。
> 为什么会复杂一些呢？因为按照我们想法💡应该是从服务端下载文件，保存在本地，然后上传时再从本地选取该文件即可。
> 而事实却是：在微信小程序中，存储文件是有一个独立的空间，虽然是在微信app的缓存中也没办法做到下载后快捷进入文件目录里。同时，官方文档中也有说明不建议查询本地文件的存储路径，因此我们只有采取其他的方式来实现。
> [微信小程序开发文档](https://developers.weixin.qq.com/miniprogram/dev/api/network/upload/wx.uploadFile.html)

## 二、处理方式
> 1、下载的文件没办法快捷进入文件所在的目录，那我们下载完就打开预览文件呢？然后通过分享，将文件保存在微信客户端的会话中
> 2、在编辑完文件之后，从微信客户端会话中选取要上传的文件即可（这样既不用查询本地文件存储路径也可以实现需求）

按照官方文档提供的API
1. 下载文件，并打开文件预览
```js
  // 下载文件-预览文件
  downloadTemp() {
    wx.downloadFile({
      url: `xxx/xxxxx/test.xlsx`,// 文件下载路径
      success (res) {
        if (res.statusCode === 200) {
          wx.openDocument({
            filePath: res.tempFilePath,
            showMenu:true, //关键点（展示右上角三个点，可将文件分享出去）
            fileType:'xlsx',
            success: function (res) {
              console.log('打开文档成功')
            }
          })
        }
      }
    })
  }
```

2. 从微信客户端会话中选取文件并上传
```js
 // 上传文件  wx.uploadFile(Object object)
  uploadPersonFile() {
    let _this = this
    wx.chooseMessageFile({// 从微信客户端会话中选取文件
      count: 1,
      type: 'file',
      extension: ['.xlsx', '.xls', '.XLSX', '.XLS', 'xlsx', 'xls', 'XLSX', 'XLS'],
      success(res) {
        const tempFilePaths = res.tempFiles // 返回的是数组，支持多文件上传，可以设置count参数
        wx.uploadFile({
          url: `xxxxxxxx/upload`,// 文件上传路径
          filePath: tempFilePaths[0].path,// 要上传文件资源的路径 (本地路径)
          name: 'doc',// 文件对应的名字 key，与服务端参数名一致即可
          formData: {
            'doc': tempFilePaths[0].path
          },
          header: {
            token: wx.getStorageSync('token'),
          },
          success(resp) {
            console.log('获取返回的数据信息',resp)
            if (resp.statusCode == 200) {
                console.log('文件上传成功')
            }
          }
        })
      }
    })
  }
```
## 三、总结

> 踩坑提示：
> 1、wx.openDocument 预览文件时，如果不指定预览文件类型 fileType:'xlsx'[文件类型值参考](https://developers.weixin.qq.com/miniprogram/dev/api/file/wx.openDocument.html) 就会出现异常提示：{errMsg: "openDocument:fail filetype not supported"}
> 2、 wx.uploadFile 上传文件时，如果有使用到 this 关键字，需要在方法前定义好变量保存 this (作用域问题)，不然可能会出现异常：TypeError: Cannot read property 'xxx' of undefined

<img src="https://s1.ax1x.com/2022/07/12/jg4omq.jpg" width="30%"/>