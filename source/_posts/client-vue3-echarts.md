---
title : Vue3 引入 Echart
date: 2022-06-30 18:04:20
img: https://s2.232232.xyz/static/384/2022/12/08-63917f425b03f.jpg
tags:
 - Vue
 - Element+
categories: 
 - 前端
 - Vue
keywords:
 - Vue
 - Vue3引入Echart并使用
---
## 一、Vue3 引入 Echart

> 🎯 Echarts 版本5+ 更新后引入方式也发生了一点小改变
> 老版本：import echarts from 'echarts'
> 5+版本：import * as echarts from 'echarts'
> 详情参照[Echarts官网](https://echarts.apache.org/handbook/zh/basics/import)

### 1. 安装echarts依赖
```
npm i echarts
```

### 2. 在页面上使用
```ts
import * as echarts from 'echarts'
```

> 注意：如果引入方式出错可能会导致echarts.init()方法报错
> 报错信息：Cannot read property ‘init’ of undefined


### 3. 定义初始化函数渲染图表
```ts
<script lang="ts" setup>
import * as echarts from 'echarts'
type EChartsOption = echarts.EChartsOption

// 饼图
const myPieLoad = () => {
    var myChart = echarts.init(document.getElementById('main'))
    var option: EChartsOption

    option = {
        dataset: [
            {
                source: [
                    ['Product', 'Sales', 'Price', 'Year'],
                    ['Cake', 123, 32, 2011],
                    ['Cereal', 231, 14, 2011],
                    ['Tofu', 235, 5, 2011],
                    ['Dumpling', 341, 25, 2011],
                    ['Biscuit', 122, 29, 2011],

                    ['Cake', 143, 30, 2012],
                    ['Cereal', 201, 19, 2012],
                    ['Tofu', 255, 7, 2012],
                    ['Dumpling', 241, 27, 2012],
                    ['Biscuit', 102, 34, 2012],

                    ['Cake', 153, 28, 2013],
                    ['Cereal', 181, 21, 2013],
                    ['Tofu', 395, 4, 2013],
                    ['Dumpling', 281, 31, 2013],
                    ['Biscuit', 92, 39, 2013],
                ]
            },
            {
                transform: {
                    type: 'filter',
                    config: { dimension: 'Year', value: 2011 }
                }
            },
            {
                transform: {
                    type: 'filter',
                    config: { dimension: 'Year', value: 2012 }
                }
            },
            {
                transform: {
                    type: 'filter',
                    config: { dimension: 'Year', value: 2013 }
                }
            }
        ],
        series: [
            {
            type: 'pie',
            radius: 50,
            center: ['50%', '25%'],
            datasetIndex: 1
            },
            {
            type: 'pie',
            radius: 50,
            center: ['50%', '50%'],
            datasetIndex: 2
            },
            {
            type: 'pie',
            radius: 50,
            center: ['50%', '75%'],
            datasetIndex: 3
            }
        ],
        media: [
            {
            query: { minAspectRatio: 1 },
            option: {
                series: [
                { center: ['25%', '50%'] },
                { center: ['50%', '50%'] },
                { center: ['75%', '50%'] }
                ]
            }
            },
            {
            option: {
                series: [
                { center: ['50%', '25%'] },
                { center: ['50%', '50%'] },
                { center: ['50%', '75%'] }
                ]
            }
            }
        ]
    }
    option && myChart.setOption(option)

}

// 当页面DOM元素挂载完毕之后开始调用饼图的初始化函数
onMounted(() => {
    myPieLoad()
})

</script>
```

> 页面html只需要放置一个div标签用来展示饼图即可

```html
<div id="main" style="width: 100%"></div>
```

> 如果出现错误信息：Initialize failed: invalid dom
> dom加载出现的问题，那么可能就是页面在dom元素还没加载完就执行了echarts.init(document.getElementById('main'))
> 而Vue3 的 setup 中直接调用，是在页面初始化时就调用了（beforeCreat / created）
> 因此强烈建议在 onMounted 中对图表进行初始化渲染，组装数据可以在页面加载时向服务端请求数据。

