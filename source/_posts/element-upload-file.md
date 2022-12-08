---
title : el-upload自定义文件上传
date: 2022-06-10
img: https://s2.232232.xyz/static/384/2022/12/08-63917f4285adf.jpg
tags:
 - Vue
 - ElementUI
categories: 
 - 前端
 - Vue
 - ElementUI
keywords:
 - 前端
 - ElementUI
 - el-upload自定义文件上传
---
## 一、为啥会有这篇烂文
> 🎯 最近没有什么可以输入的博客，但是又不想持续断更，所以就有了👇下面这篇文章来凑字数了
> 今天写点常见的问题吧！文件上传，虽然UI框架已经提供给我们很完善的文件上传组件了，但是大多数时候我们还是需要自定义上传方法来实现具体的业务需求
> 看完第二句话其实，下文就可以省略了。（如果您正好有空，愿意赏光看完下面的废话文字，小编表示万分的感谢🙏）

## 二、回归正题
### 1. element官网API
老规矩先来贴一个官网链接：[ElementUI 文件上传](https://element.eleme.cn/#/zh-CN/component/upload)
有需要的自行下单哈哈哈哈哈

### 2. 具体实现
> 1、配置好组件的必要属性值，比如：action、accept、auto-upload、on-change等
> 2、自定义文件上传、提交方法，覆盖 http-request 对应的上传方法
> 3、实现自定义的方法
> 4、如果是批量上传则需要配合，on-change、on-remove来获取上传文件list。因为el-upload是通过模拟表单来上传文件的，所以需要我们new FormData() 将文件列表append到表单中再提交

```
  <el-upload
    accept=".jpg,.png,.jepg,.bmp"
    class="avatar-uploader"
    action="api/picture/xxx"
    :show-file-list="false"
    :http-request="uploadSectionFile"
    :auto-upload="true"
    :on-change="handleChange"
    :file-list="fileList"
  >
    <img :src="imageUrl" class="avatar"/>
	</el-upload>
  <el-button type="primary" @click="submitUpload">提交</el-button>
```

```js
// 添加文件【文件状态改变时的钩子，添加文件、上传成功和上传失败时都会被调用】
handleChange(file, fileList) {
  var obj = {};
  fileList = fileList.reduce(function(item, next) {
    obj[next.name] ? '' : obj[next.name] = true && item.push(next);
    return item;
  }, []);
  this.imageUrl = URL.createObjectURL(file.raw);
},
// 自定义上传方法
uploadSectionFile(param) {
  let fileObj = param.file;
  this.formObj = new FormData();
  this.formObj.append('image', fileObj);
},
// 提交上传图片
submitUpload() {
  this.$http.post(process.env.API_HOST + `picture/xxx?params1=${this.params1}&color=+${this.color}`, this.formObj).then(res => {
    if (res.data.result_code == 200) {
      // 图片上传成功
      console.log(res.data.url)
    }else {
      // 上传失败
    }
  });
},

```

> a、auto-upload 是否在选取文件后立即进行上传，默认为true。上传文件后会自动触发上传方法
> b、多文件上传，如果将提交方法在自定义上传 http-request 中实现，会按照文件数量发送多次请求。（如果是同一个文件点击多次提交，发送请求也只有第一次点击时出发）一次提交上传多个文件需要将其上传与提交分开实现，上传文件处理文件list列表，提交上传则将参数一次提交到后台处理


![](https://s4.ax1x.com/2022/02/11/HU5hu9.jpg)
