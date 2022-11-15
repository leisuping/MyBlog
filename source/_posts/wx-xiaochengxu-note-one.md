---
title : 微信小程序
date: 2022-09-30 18:47:34
img: https://s6.jpg.cm/2021/12/07/LQcetR.png
tags:
 - 微信小程序
categories: 
 - 微信小程序
 - js
keywords:
 - 微信小程序
---
## 一、引题
> 🎯 前言：之前更多的都是偏向web前端的内容，这次也来简单了解一下移动端的小程序（对比一下）
> [微信官方文档](https://developers.weixin.qq.com/miniprogram/dev/framework/)

## 二、内容
> 小程序的相关文件类型、项目结构等我就不一一解释了，[微信官方文档](https://developers.weixin.qq.com/miniprogram/dev/framework/)都有很详细的说明哦！

### 1、小程序的优劣势
> 优势：
> 1、即用即走，不用安装，省流量，省安装时间，不占用桌面
> 2、依托微信流量，有推广传播优势
> 3、开发成本比 App 低

> 缺点：
> 1、限制较多,页面大小不能超过2M(可分包)
> 2、页面栈不能打开超过10个层级的页面

### 2、小程序的生命周期
> onLoad() 页面加载时触发。一个页面只会调用一次，可以在 onLoad 的参数中获取打开当前页面路径中的参数
> onShow() 页面显示时/切入前台时触发。
> onReady()页面初次渲染完成时触发。一个页面只会调用一次，代表页面已经准备妥当，可以和视图层进行交互
> onHide() 页面隐藏/切入后台时触发。 如 navigateTo 或底部 tab 切换到其他页面，小程序切入后台等
> onUnload()页面卸载时触发。如 redirectTo 或 navigateBack 到其他页面时

### 3、wx.navigateTo(), wx.redirectTo(), wx.switchTab(), wx.navigateBack(), wx.reLaunch()的区别
> wx.navigateTo() 保留当前页面，跳转到应用内的某个页面。但是不能跳到 tabbar 页面。使用 wx.navigateBack 可以返回到原页面。小程序中页面栈最多十层
> wx.redirectTo() 关闭当前页面，跳转到应用内的某个页面。但是不允许跳转到 tabbar 页面。
> wx.switchTab() 跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面
> wx.navigateBack() 关闭当前页面，返回上一页面或多级页面。可通过 getCurrentPages 获取当前的页面栈，决定需要返回几层
> wx.reLaunch() 关闭所有页面，打开到应用内的某个页面

### 4、小程序的双向绑定和vue有什么不一样?
vue数据的双向绑定是可以直接同步到视图的，而小程序的双向绑定必须通过this.setData({属性名:属性值})来设置

### 5、小程序页面数据传递方式
#### 1)使用globalData全局变量传递数据
> 在 app.js 文件中定义全局变量 globalData， 将需要存储的信息存放在里面

```js
// app.js
App({
  onShow(options) {
    // 获取其他小程序跳转过来携带的参数
    this.globalData.loginSystem = {
        user: options.referrerInfo.extraData.user,
        passwd: options.referrerInfo.extraData.passwd,
    }
  },
  onLaunch(options) {

  },
  globalData: {
    loginSystem:{
      user: '',
      passwd: '',
    },
    userInfo: null,
  },
})

```

```js
// 在页面js使用时直接使用 getApp() 拿到存储的信息
let app = getApp()
app.globalData.loginSystem.user
app.globalData.loginSystem.passwd
```

#### 2)使用路由传值
> 比如：wx.navigateTo 与 wx.redirectTo跳转页面时，将部分数据放在url后面
```js
// 示例
wx.navigateTo({
    url: `/pages/index/index?name=${params.name}&passwd=${params.passwd}&userObj=${JSON.stringify(user)}`,
})

// 在index页面的js中，获取参数
onLoad: function (options) {
    let name = options.name
    let passwd = options.passwd
    let user = JSON.parse(options.userObj)
}
// 注意：wx.navigateTo 和 wx.redirectTo 不允许跳转到 tab 所包含的页面 onLoad 只执行一次
```

#### 3)使用本地缓存 Storage
```js
// 暂存
wx.setStorageSync('user', userObj)
// 获取
let user = wx.getStorageSync('user')
// 其他参照官方文档
```


### 6、小程序事件传值
> 页面标签上通过 绑定 dataset-key = value ， 然后绑定点击通过e.currentTarget.dataset.key 来获取标签上绑定的值
```js
<van-button type="primary" bindtap="handelDetail" data-item="{{item}}">事件传值</van-button>

// 获取点击事件的对象
handelDetail(event) {
  let {id,name} = event.target.dataset.item
}

```


### 7、wx:if 和 hidden 的区别
> 类似于vue的 v-if 与 v-show
> wx:if 有更高的切换消耗，运行时条件变化使用
> hidden 有更高的初始渲染消耗，频繁切换使用


### 8、如何实现下拉刷新
#### 1）通过 enablePullDownRefresh 配置开启全局下拉刷新
> 1、在全局 app.json 配置 enablePullDownRefresh 为 true (如果只在单个页面开启，就在对应的页面json中配置)
> 2、在 Page 中定义 onPullDownRefresh 钩子函数,到达下拉刷新条件后，该钩子函数执行，发起请求方法
> 3、请求返回后，调用 wx.stopPullDownRefresh 停止下拉刷新
```js
onPullDownRefresh: function () {
    wx.showNavigationBarLoading() //启用标题栏显示加载状态
    this.onShow() //调用相关方法
    setTimeout(() => {
      wx.hideNavigationBarLoading() //隐藏标题栏显示加载状态
      wx.stopPullDownRefresh() //停止下拉刷新
    }, 2000); //设置执行时间
},
```

#### 2）通过 scroll-view 滚动组件实现自定义刷新
> 通过组件的 bindscrolltoupper 属性滚动到顶部时触发 scrolltoupper 事件实现刷新功能


### 9、bindtap和catchtap的区别
> 相同点：点击事件函数
> 不同点：bindtap 不会阻止冒泡，catchtap 阻止冒泡

### 10、登录流程
> 1、调wx.login获取code传给后台服务器获取微信用户唯一标识openid及本次登录的会话密钥（session_key）
> 2、获取到开发者服务器传回来的会话密钥（session_key）之后，前端可保存wx.setStorageSync('sessionKey', 'value')持久登录状态：session信息存放在cookie中以请求头的方式带回给服务端，放到request.js里的wx.request的header里

```js
wx.login({
  success: res => {
    var params = {//请求参数
      wx_code: res.code,//用户登录凭证（有效期五分钟）
    }
    // 看需要是否要请求服务端（可选）
    http.postRequest("user/wxlogin", params, res => {
      if(res.result_msg== "请先登录"){
        var pages = getCurrentPages() //获取加载的页面
        var currentPage = pages[pages.length-1] //获取当前页面的对象
        var url = currentPage.route //当前页面url   
        var skipUrl = ""
        for(let I = 1; I < url.split("/").length-1; I++){
          skipUrl = skipUrl + '../'
        }
        wx.reLaunch({
          url: skipUrl+'login/login'
        })
      }else{
        wx.setStorageSync("token",config.token)
        resolve()
      }
    }, res => {
      console.log("请求登录失败了")
    })
  }
})
```

### 11、如何封装小程序请求
> 以下内容来源于网络
```js
const app = getApp(); 

/**@param {*} url 请求路径
 * @param {*} options 请求参数
 * @param {*} isNeedTocken 是否需要Tocken
 * @param {*} isJson 是否已json形式传给后台, 否则仪表单的形式
*/
const request = (url, options,isNeedTocken,isJson) => {
   return new Promise((resolve, reject) => {
    wx.request({
      url: `${ app.globalData.host}${ url}`,//拼接请求路径，读取app.js中的全局变量
      method: options.method,
      data:isJson?JSON.stringify(options.data):options.data ,
      header: {
        'Content-Type':isJson?'application/json':'application/x-www-form-urlencoded',
        'authorization': isNeedTocken?app.globalData.tocken:''  // 看自己是否需要
      },
      success(request) {
        if (request.data.code === 200) {//请求成功的状态码
          resolve(request.data)
        } else {
          reject(request.data)
        }
      },
      fail(error) {
        reject(error.data)
      }
    })
  })
}

/** *封装get请求 */
const get = (url, options = {},isNeedTocken = true,isJson = false) => {
  return request(url, {method: 'GET', data: options },isNeedTocken,isJson)
}
/** * 封装post请求 */
const post = (url, options,isNeedTocken=true,isJson = false) => {
  return request(url, {method: 'POST', data: options },isNeedTocken,isJson)
}

//暴露接口
module.exports = {
  get,
  post
}





// 调用接口
import req from "../../api/request"

function testPost = () => {
  req.post(login,{"phoneOrNumber":phoneOrNumber,"possword":possword},false).then(res =>{
    //处理成功的请求
  }).catch(err=>{
    //请求报错，或者状态码返回错误。
  })
}
```

















