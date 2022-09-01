---
title : vue-cropper图片裁剪
date: 2022-09-01 16:47:34
img: https://s6.jpg.cm/2021/12/07/LQcetR.png
tags:
 - vue
 - vue-cropper图片裁剪
categories: 
 - vue
 - js
keywords:
 - vue
 - vue-cropper
---
## 一、引题
> 🎯 前言：前端页面很多都涉及到图片裁剪的问题，开源的图片裁剪插件也挺多的。这次简单来讲一下Vue-Cropper吧
> [vue-cropper GitHub地址](https://github.com/xyxiao001/vue-cropper)

## 二、实现
### 1、安装
```bash
# npm 安装
npm install vue-cropper

# yarn 安装
yarn add vue-cropper

```
### 2、引用
```js
// Vue 3 引用
// 全局引用
import VueCropper from 'vue-cropper'; 
import 'vue-cropper/dist/index.css'

const app = createApp(App)
app.use(VueCropper)
app.mount('#app')

// 组建内引用
npm install vue-cropper@next
import 'vue-cropper/dist/index.css'
import { VueCropper }  from "vue-cropper";

// Vue 2 引用
// 全局引用
import VueCropper from 'vue-cropper'
Vue.use(VueCropper)

// 组建内引用
import { VueCropper }  from 'vue-cropper' 
components: {
  VueCropper
}

// CDN 方式引入 与 nuxt 引入方式 参见GitHub上的使用方式

```

### 3、页面上的使用
```html
<template>
    <div class="cropper-wrapper">
		<div class="header-icon">
			<i class="el-icon-arrow-left" @click="$router.push({path: '/department/list'})" style="font-size: 25px;font-weight: bold;cursor: pointer;color: #419fff;"><span style="font-size: 20px;">返回</span></i>
		</div>
        <div style="text-align: center;">
            <el-upload class="upload-demo"
				accept=".jpg,.png,.jepg,.bmp"
				action=""
				drag
				:auto-upload="false"
				:show-file-list="false"
				:on-change='handleChangeUpload'>
                <i class="el-icon-upload"></i>
                <div class="el-upload__text">点击上传</div>
                <div class="el-upload__tip">支持.jpg,.png,.jepg,.bmp图片，单张图片最大支持2MB</div>
            </el-upload>
            <el-button type="primary" plain @click="uploadImageToServer" :loading="addLoading" v-show="previewImgList.length>0" style="margin-left:25px">上传到服务器</el-button>
            
        </div>
        <div class="pre-box">
			<el-col :span="24" style="display: flex; flex-flow:row wrap;">
				<el-col
					v-for="(item, index) in previewImgList"
					:key="index"
					style="padding: 15px; position: relative;"
					:span="4"
					@mouseenter.native="menuShow(item)"
					@mouseleave.native="HideMenu(item)"
				>
					<div style="width: 100%;" class="img_div">
						<img :src="item.blobUrl" alt="" style="width: 100%;"/>
						<div class="imgOptionMenu" v-if="item.isMenuShow">
							<i class="el-icon-delete" style="font-size: 40px;color: white;margin:40% 40%" @click="deleteImage(index)"></i>
						</div>
					</div>
				</el-col>
			</el-col>
        </div>
        <!-- vueCropper 剪裁图片实现-->
        <el-dialog title="图片剪裁" :visible.sync="dialogVisible" class="crop-dialog" append-to-body width="70%">
            <div class="cropper-content">
                <div class="cropper" style="text-align:center">
                    <vueCropper
                        ref="cropper"
                        :img="option.img"
                        :outputSize="option.size"
                        :outputType="option.outputType"
                        :info="option.info"
                        :full="option.full"
                        :canMove="option.canMove"
                        :canMoveBox="option.canMoveBox"
                        :original="option.original"
                        :autoCrop="option.autoCrop"
                        :fixed="option.fixed"
                        :fixedNumber="option.fixedNumber"
                        :centerBox="option.centerBox"
                        :infoTrue="option.infoTrue"
                        :fixedBox="option.fixedBox"
                        :autoCropWidth="option.autoCropWidth"
                        :autoCropHeight="option.autoCropHeight"
                        :high="option.high"
                        @cropMoving="cropMoving"
                    />
                </div>
            </div>
            <div class="action-box">
                <el-upload class="upload-demo"
                           action=""
                           :auto-upload="false"
                           :show-file-list="false"
                           :on-change='handleChangeUpload'>
                    <el-button type="primary" plain>更换图片</el-button>
                </el-upload>
                <el-button type="primary" plain @click="clearImgHandle">清除图片</el-button>
                <el-button type="primary" plain @click="rotateLeftHandle">左旋转</el-button>
                <el-button type="primary" plain @click="rotateRightHandle">右旋转</el-button>
                <el-button type="primary" plain @click="changeScaleHandle(1)">放大</el-button>
                <el-button type="primary" plain @click="changeScaleHandle(-1)">缩小</el-button>
                <el-button type="primary" plain @click="downloadHandle('blob')">下载</el-button>
            </div>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="finish" :loading="loading">确认</el-button>
            </div>
        </el-dialog>
    </div>
</template>
```

> 页面js
```js
export default {
    name: "Cropper",
    data() {
        return {
          dialogVisible: false,
          previewImgList: [],// 图片预览地址
          // 裁剪组件的基础配置option
          option: {
              img: 'https://pic1.zhimg.com/80/v2-366c0aeae2b4050fa2fcbfc09c74aad4_720w.jpg', // 裁剪图片的地址
              info: true, // 裁剪框的大小信息
              outputSize: 1, // 裁剪生成图片的质量
              outputType: 'png', // 裁剪生成图片的格式
              canScale: true, // 图片是否允许滚轮缩放
              autoCrop: true, // 是否默认生成截图框
              canMoveBox: true, // 截图框能否拖动
              autoCropWidth: 600, // 默认生成截图框宽度
              autoCropHeight: 400, // 默认生成截图框高度
              fixedBox: false, // 固定截图框大小 不允许改变
              fixed: false, // 是否开启截图框宽高固定比例
              fixedNumber: [1, 1], // 截图框的宽高比例
              full: false, // 是否输出原图比例的截图
              original: true, // 上传图片按照原始比例渲染
              centerBox: false, // 截图框是否被限制在图片里面
              infoTrue: true, // true 为展示真实输出图片宽高 false 展示看到的截图框宽高
              high: false,//是否按照设备的dpr 输出等比例图片【注意这个属性非常重要‼️】
          },
			    addLoading: false,
          // 防止重复提交
          loading: false
        }
    },
	mounted() {
    // 页面渲染完
	},
  methods: {
    // 上传按钮 限制图片大小和类型
    handleChangeUpload(file, fileList) {
      const isJPG = file.raw.type === 'image/jpeg';
      const isGIF = file.raw.type === 'image/gif';
      const isPNG = file.raw.type === 'image/png';
      const isBMP = file.raw.type === 'image/bmp';

      const isLt2M = file.size / 1024 / 1024 < 2;
      if (!isJPG && !isGIF && !isPNG && !isBMP) {
          this.$message.error('上传图片必须是JPG/GIF/PNG/BMP 格式!');
          return false
      }
      if (!isLt2M) {
          this.$message.error('上传头像图片大小不能超过 2MB!');
          return false
      }
      // 上传成功后将图片地址赋值给裁剪框显示图片
      this.$nextTick(async () => {
          // base64方式
          // this.option.img = await fileByBase64(file.raw)
          this.option.img = URL.createObjectURL(file.raw)
          this.loading = false
          this.dialogVisible = true
      })
    },
    // 放大/缩小
    changeScaleHandle(num) {
        num = num || 1;
        this.$refs.cropper.changeScale(num);
    },
    // 左旋转
    rotateLeftHandle() {
        this.$refs.cropper.rotateLeft();
    },
    // 右旋转
    rotateRightHandle() {
        this.$refs.cropper.rotateRight();
    },
    // 下载
    downloadHandle(type) {
        let aLink = document.createElement('a')
        aLink.download = 'author-img'
        if (type === 'blob') {
            this.$refs.cropper.getCropBlob((data) => {
                aLink.href = URL.createObjectURL(data)
                aLink.click()
            })
        } else {
            this.$refs.cropper.getCropData((data) => {
                aLink.href = data;
                aLink.click()
            })
        }
    },
    // 清理图片
    clearImgHandle() {
        this.option.img = ''
    },
    // 截图框移动回调函数
    cropMoving(data) {
        // 截图框的左上角 x，y和右下角坐标x，y
        // let cropAxis = [data.axis.x1, data.axis.y1, data.axis.x2, data.axis.y2]
        // console.log(cropAxis)
    },
    finish() {
      // 获取截图的 blob 数据
      this.$refs.cropper.getCropBlob((blob) => {
        this.loading = true
        this.dialogVisible = false
        this.previewImgList.push({blobUrl:URL.createObjectURL(blob),imgBlob: blob,isMenuShow: false})
      })
      // 获取截图的 base64 数据
      // this.$refs.cropper.getCropData(data => {
      //     console.log(data)
      // })
    },
    menuShow(param) {
      param.isMenuShow = true;
    },
    HideMenu(param) {
      param.isMenuShow = false;
    },
    deleteImage(index) {
      this.previewImgList.splice(index,1);
    },
    getFileNameStr(imgBlob) {
        let infoArr = imgBlob.type.split('/')
        let str = new Date().getFullYear()+'/'+new Date().getMonth()+'/'+new Date().getDate()+'图'+Math.floor(Math.random()*100000) +'.'+ infoArr[1]
        return str
    },
    async uploadRequest() {
      let desc = ''
      this.addLoading = true;
      for (let i = 0; i < this.previewImgList.length; i++) {
        let str = this.getFileNameStr(this.previewImgList[i].imgBlob)
        let form = new FormData();
        form.append('image', this.previewImgList[i].imgBlob,str);
            // console.log('图片数据',this.previewImgList)
            let uploadRes = await this.$http
                .post(process.env.API_HOST + 'picture/upload?token=' + sessionStorage.getItem('token') + '&desc=' + desc, form)
            if (uploadRes.data.result_code !== 200) {
                console.log('上传失败了')
            }
        }
        this.addLoading = false;
        this.previewImgList = []
        this.$router.push({path: '/picture/list'})
    },
    uploadImageToServer() {
      this.uploadRequest();// 上传到服务器
    },
  }
}

```

> 页面CSS样式
```css
.cropper-wrapper {
    justify-content: center;
    align-items: center;
    .pre-box {
        display: flex;
        button {
            width: 100px;
            margin-top: 15px;
        }
    }
}

.crop-dialog {
    .cropper-content {
        padding: 0 40px;
        .cropper {
            width: auto;
            height: 500px;
            max-width: 1400px;
            max-height: 900px;
        }
    }

    .action-box {
        padding: 25px 40px 0 40px;
        display: flex;
        justify-content: center;

        button {
            width: 80px;
            margin-right: 15px;
        }
    }

    .dialog-footer {
        button {
            width: 100px;
        }
    }
}
.imgOptionMenu{
	position: absolute;
    top: 5%;
    left: 5%;
    width: 90%;
    height: 90%;
    background: rgba(156, 155, 155, 0.6);
    color: #ffffff;
    opacity: 0;
    z-index: 999;    
}
.img_div:hover .imgOptionMenu {
    opacity: 1;
}
```

### 4、效果预览

![](./pic_cropper.jpg)

![](./pic_del.jpg)

> 为了图片裁剪更精准，其中几个重要属性值建议如下：
> 1、high 是否按照设备的dpr 输出等比例图片，默认是true。建议使用false（原图比例输出到页面即可）
> 2、original 上传图片按照原始比例渲染，默认为false。建议使用true（按照原始图片大小渲染）
> 3、infoTrue true 为展示真实输出图片宽高 false 展示看到的截图框宽高，默认为false。建议使用true（展示真实的图片宽高）

<img src="https://s1.ax1x.com/2022/07/12/jg4omq.jpg" width="30%"/>