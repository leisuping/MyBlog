---
title : Element 自定义表单验证
date: 2021-11-29
tags:
 - Vue
 - ElementUI
 - form
categories: 
 - 前端
 - Vue
 - ElementUI
keywords:
 - 前端
 - Vue
 - 自定义表单验证
---

## 一、需求
> 使用官网提供的表单验证，实现表单数据的自定义校验
> 除了非空校验，可能还有一些业务相关的数据校验，需要用到自定义表单验证
> 自定义正则校验、调用接口校验数据等等

## 二、处理方式

> 在form表单中绑定 rules 属性
> 在表单数据项中用 prop 绑定每个数据项校验规则

```html
<el-form
  id="myForm"
  :inline="true"
  :model="myForm"
  ref="myForm"
  :rules="rules"
  label-width="100px"
>
    <el-form-item
      prop="xzPerson"
      class="form-input"
      label="名称"
    >
      <el-input
        clearable
        v-model="myForm.xzPerson"
        placeholder="填写名称"
      >
      </el-input>
  </el-form-item>
  <el-form-item
    prop="xzType"
    class="form-input"
    label="类型"
  >
    <el-select
      v-model="myForm.xzType"
      placeholder="请选择类型"
    >
      <el-option
        v-for="item in typeList"
        :key="item.id"
        :label="item.name"
        :value="item.id"
      >
      </el-option>
    </el-select>
  </el-form-item>
  <!-- 
    表单设置了rules规则，表单中的每个数据项也可以单独设置数据校验规则：
    当myForm.xzType为空或者为指定值时，xzXydm非必填[{required: false}]，否则就按表单定义的校验规则
  -->
  <el-form-item
    prop="xzXydm"
    :rules="(myForm.xzType === '' || myForm.xzType === 'xxx') ? [{required: false}] : rules.xzXydm"
    class="form-input form-label-max-length"
  >
    <!-- 如果标题过长，可以自定义换行 -->
    <label slot="label">xxxxxx_1<br/>(xxxxx代码)</label>
    <el-input
      v-model="myForm.xzXydm"
      style="width: 100%"
      clearable
      placeholder="提示信息"
    >
    </el-input>
  </el-form-item>
  <el-form-item
    prop="xzGszch"
    class="form-input form-label-max-length"
  >
    <label slot="label">xxxxx_2<br/>(xxxxxx)</label>
    <el-input
      v-model="myForm.xzGszch"
      :disabled="(myForm.xzType === '' || myForm.xzType === 'xxx') ? true : false"
      style="width: 100%"
      clearable
      placeholder="提示信息"
    >
    </el-input>
  </el-form-item>
</el-form>

```
> 在js中定义并初始化表单数据
> 定义好 myForm 以及 rules 相关数据
> 自定义验证参考官方文档使用 validator 实现，其中对应的验证方法必须写在 data 函数 return 的外面

> 错误信息通过 return callback(new Error('错误信息')) 在页面上提示
> 数据正常 callback() 中不new Error
> 接口校验的方法，需要加上async修饰符，方法内部可直接通过 await 接口名称({ 参数: value }) 获取接口返回值进行判断（处理异步请求）

```js
// 引入外部接口校验
import {
  checkXydm,
} from "@/api/publicity.js";
import {
  getStrLength,
} from "@/utils/common";
// 引入外部正则校验
import {
  regSpecialSymbol,
  regNotNum,
  regSocialCreditCode,
  regGszcCode,
} from "@/utils/reg";
// 初始化页面数据
export default {
  data() {
    // 自定义验证 start
    let _this = this;
    // 名称
    let validateXzPerson = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入名称'));
      }
      if (!regSpecialSymbol.test(value) || !regNotNum.test(value)) {
        return callback(new Error('名称不得包含数字、*或null或test或中英文问号等特殊字符'));
      }
      if (getStrLength(value) < 3 || getStrLength(value) > 50) {
        return callback(new Error('名称长度不能超过50个字符且必须大于一个汉字或大于三个字符'));
      }
      callback();
    };
    // xxxx代码
    let validatorXzXydm = async (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入xxx代码'));
      }
      if (value && !regSocialCreditCode.test(value)) {
        return callback(new Error('xxxx代码填写错误'));
      }
      let res = await checkXydm({ xydm: value });
      if (!res.data) {
        return callback(new Error('xxxx代码验证有误'));
      }
      callback();
    };
    // 注册号
    let validatorXzGszch = (rule, value, callback) => {
      if (value && !regGszcCode.test(value)) {
        return callback(new Error('注册代码填写错误'));
      }
      callback();
    }
    // 自定义验证 end
    return {
      myForm: {
        xzPerson: "",
        xzType: "",
        xzXydm: "",
        xzGszch: "",
      },
      // 表单校验
      rules: {
        xzPerson: [
          {
            required: true,
            validator: validateXzPerson,
            trigger: 'blur',
          }
        ],
        xzType: [ // 普通的非空验证
          { required: true, message: '请输入类型', trigger: 'blur' },
        ],
        xzXydm: [
          {
            required: true,
            validator: validatorXzXydm,
            trigger: 'blur',
          },
        ],
        xzGszch: [
          { 
            validator: validatorXzGszch,
            trigger: "blur",
          },
        ],
      },
    },
  }
}
```
> OK完美结束！

<img src="./imagepic.jpg" style="width: 400px"/>