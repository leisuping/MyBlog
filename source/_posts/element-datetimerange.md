---
title : element日期组件时间校验精确到时分秒
date: 2024-02-29 12:34:00
img: https://s11.ax1x.com/2024/02/29/pFwtwiF.jpg
tags:
 - element
 - DateTimePicker
categories: 
 - vue
 - element
keywords:
 - element日期组件
 - DateTimePicker时间校验
---

> DateTimePicker 日期时间选择器，能够选择开始时间-结束时间（精确到时分秒）
> 在项目中也使用的比较多了，如果需要对选择的日期时间有条件限制就要在该组件提供的事件中自定义了
> [Element DateTimePicker](https://element.eleme.cn/#/zh-CN/component/datetime-picker)


## 1.Element DateTimePicker 日期组件
> default-time	选择日期后的默认时间值。 如未指定则默认时间值为 00:00:00
> picker-options	当前时间日期选择器特有的选项，disabledDate 可设置禁用时间，onPick 选择日期触发的事件
> change 用户确认选定值触发（如果选择日期事件并未达到预期的效果，可以在确认选定值时再次进行校验）


```html
<!-- 日期控件 -->
<el-form-item label="时间" :rules="[{ required: true, message: '请选择时间', trigger: 'blur' }]" label-width="150px">
  <el-date-picker
  v-model="setTime"
  ref="datetime"
  type="datetimerange"
  :picker-options="pickerOptions"
  :default-time="defaultTime"
  value-format="timestamp"
  @change="handelTimeChange"
  :clearable="false"
  range-separator="-"
  start-placeholder="开始时间"
  end-placeholder="结束时间">
  </el-date-picker>
</el-form-item>

```


## 2.组件js部分相关事件
> nowTime(n):获取当前系统时间；n 为分钟 int类型，标识取当前时间后 n 分钟。[参考文章](https://leisuping.github.io/MyBlog/jsnote-one/)
> datajs 是一个新的跨浏览器 JavaScript 库，它通过利用 JSON 和 OData 等现代协议以及支持 HTML5 的浏览器功能来支持以数据为中心的 Web 应用程序。
> datajs 通过 'npm i datajs' 将其引入到项目中。

```html
<script>
  import { nowTime } from '@/utils/util'// nowTime:当前时间
  import dayjs from 'dayjs';
	const DateFormat = 'YYYY-MM-DD'
	const TimeFormat = 'HH:mm:ss'
	const StartTimeFormat = '00:00:00'
	const ENDTimeFormat = '23:59:59'
  const startMinute = 1
  const endMinute = 11
	export default {
		props: {
			range: {
				type: Array,
				default: () => [nowTime(startMinute), '']
			}
		},
		data() {
			return {
				setTime: [new Date(nowTime(startMinute)).getTime(), new Date(nowTime(endMinute)).getTime()],
				defaultTime: [StartTimeFormat, ENDTimeFormat],// 日期选择框默认时间
				pickerOptions: {// 设置时间限制
					disabledDate: time => {
						const [start, end] = this.range
						const d = new Date(time).getTime()
						const pickDate = dayjs(time).format(DateFormat)
						const startDate = dayjs(start).format(DateFormat)
						const startTime = dayjs(start).format(TimeFormat)
						const startStamp = new Date(start).getTime()
						const endStamp = new Date(end).getTime()
						// 组件（界面）上的日期 和 时间范围中最小的日期  相同时
						if (pickDate === startDate && startTime !== ENDTimeFormat) {
							return false
						}
						// 这里不用判断时间范围中最大的日期
						return d < startStamp || d >= endStamp // 小于最小日期 或者 大于最大日期
					},
					onPick: ({ minDate, maxDate }) => {
						if (!(minDate && maxDate)) {// 选择完整的日期后，再进入后面的逻辑
							return
						}
						const picker = this.$refs.datetime? this.$refs.datetime.picker : null
						const { minTimePicker, maxTimePicker } = picker.$refs
						const [start, end] = this.range
						const pickStartDate = dayjs(minDate).format(DateFormat) // 手动选择的最小日期
						const pickEndDate = dayjs(maxDate).format(DateFormat) // 手动选择的最大日期
						const startDate = dayjs(start).format(DateFormat) // 时间范围this.range，最小日期 年月日
						const startTime = dayjs(start).format(TimeFormat) // 时间范围this.range，最小时间 时分秒
						const endDate = dayjs(end).format(DateFormat) // 时间范围this.range，最大日期 年月日
						const endTime = dayjs(end).format(TimeFormat) // 时间范围this.range，最大日期 年月日
						// 默认可以选择全部的时间范围，不做限制
						minTimePicker.selectableRange = []
						maxTimePicker.selectableRange = []
						// 手动选择的最小日期 等于 时间范围this.range的最小日期，那么需要限制时间范围
						if (pickStartDate === startDate) {
							minTimePicker.selectableRange = [
								[new Date(`${startDate} ${startTime}`), new Date(`${startDate} ${ENDTimeFormat}`)]
							]
						}
						if (pickEndDate === startDate) {
							maxTimePicker.selectableRange = [
								[new Date(`${startDate} ${startTime}`), new Date(`${startDate} ${ENDTimeFormat}`)]
							]
						}
					}
				},
			};
		},
		methods: {
			// input事件, 输入结束后0.5秒触发
			handelInputChange() {
				let that = this
				if (that.showForm.template_id) {
					clearInterval(that.timmer)
					that.timmer = setInterval(()=>{
						that.lookForward();
						// 事件触发后,清除定时器
						clearInterval(that.timmer)
					}, 500)
				}
			},
			// 时间change事件
			handelTimeChange(val) {
				// 判断时间是否符合要求
				if (dayjs().isAfter(dayjs(val[0]))) {
					this.$message({
						type: 'warning',
						showClose: true,
						message: this.$t('meeting.meetingTimeAfter'),
						duration: 2000,
					});
					this.showForm.start = 0
					this.showForm.end = 0
					this.setTime = []
					return
				}
				this.showForm.start = parseInt(val[0]/1000)
				this.showForm.end = parseInt(val[1]/1000)
				this.showModel()
			},
		},
		mounted() {
		}
	};
</script>
```
