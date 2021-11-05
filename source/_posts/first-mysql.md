---
title: MySql日期函数的使用与批量更新
date: 2019-05-17 17:30:45
tags:
 - MYSQL
categories: 
 - 后端
 - 数据库
---

&emsp;目前MySQL版本已经更新到8.0.15，但是大部分企业中使用的还是MySQL5.7的。我本地安装的是MySQL5.7.20，因为MySQL版本的不同可能对某些SQL语句的支持度也不同，所以特地在此说明。

### 最近使用到MySQL中这两个日期函数：
1、date_sub(date，interval num 单位); 返回date减去num 时间间隔后的时间

```
例如：
select date_sub(date, interval 1 year);
select date_sub(date, interval 1 quarter);
select date_sub(date, interval 1 month);
select date_sub(date, interval 1 week);
select date_sub(date, interval 1 day);  等同于 date_add(date, interval -1 day);
select date_sub(date, interval 1 hour);
select date_sub(date, interval 1 minute);
select date_sub(date, interval 1 second);
select date_sub(date, interval 1 microsecond);

```

注：date_sub(date，interval num 单位) 里面的 num 是数字类型的参数，单位则是 年(year)、半年(quarter)、月(month)、日(day)、周(week)、小时(hour)、分钟(minute)、秒(second)、毫秒(microsecond)。【与之对应的还有 date_add() 函数用法与 date_sub() 一样】

2、datediff(date1,date2); 返回date1减去date2的差值

```
例如：
select datediff('2019-05-17', '2019-05-11'); -- 6
```

如果使用这两个函数来查询最近一周、一个月、半年、一年...的数据会有什么不同的效果呢？

1、使用函数 date_sub()：

```
select date_sub(date, interval 1 day); -- 当天
select date_sub(date, interval 7 day); -- 最近一周
select date_sub(date, interval 30 day); -- 最近一个月
select date_sub(date, interval 365 day); -- 最近一年
......

```

2、使用函数 datediff():

```
第一个参数作为动态参数输入，第二个为数据库中的时间字段
select datediff('2019-05-17', '2019-05-11') <= 7 ; -- 最近一周

显然是没有问题的，但是当第二个参数的天数大于第一个参数的天数时则会出现问题。

例如：select datediff('2019-05-01', '2019-04-11') <= 7 ;
     select datediff('2019-05-01', '2019-05-11') <= 7 ;

这两条语句会使得时间范围判断无效!!!!

```

总结：函数 date_sub() 与 date_add() 都可以做时间范围的判断，而 datediff() 更适用做时间差。这几个函数各司其职，大大简化了数据库的时间操作。

### 关于MySQL语句的批量更新操作

一般需要用到批量添加或者更新的，我们都会想到用循环迭代。sql中使用when ... then ...

```
<!-- 批量修改 -->
<update id="updateBatch" parameterType="java.util.List">
     update hnca_licetype set
     PARAM_TOP =
     <foreach collection="list" item="licetypes" index="index"
                    separator=" " open="case LICETYPEID" close="end">
          when #{licetypes.lICETYPEID} then #{licetypes.pARAMTOP}
     </foreach>
     ,PARAM_LEFT =
     <foreach collection="list" item="licetypes" index="index"
                    separator=" " open="case LICETYPEID" close="end">
          when #{licetypes.lICETYPEID} then #{licetypes.pARAMLEFT}
     </foreach>
     where LICETYPEID in
     <foreach collection="list" item="licetypeIds" index="index"
                    separator="," open="(" close=")">
          #{licetypeIds.lICETYPEID}
     </foreach>
</update>

```







参考 https://www.cnblogs.com/ggjucheng/p/3352280.html