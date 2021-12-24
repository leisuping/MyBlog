---
title : MySQL页面上时间展示与数据库中时间相差8个小时
date: 2019-05-17 17:00:00
img: https://s6.jpg.cm/2021/12/07/LQebqW.jpg
tags:
 - MYSQL
 - Date
 - Spring
categories: 
 - 后端
 - 数据库
keywords:
 - 后端
 - MySQL页面上时间展示与数据库中时间相差8个小时
 - 数据库
---
尝试一下几种方法：

方法一：
```java
@JsonFormat(pattern="yyyy-MM-dd  HH:mm:ss" ,  timezone="GMT+8")
private  Date  createTime;
```

方法二：
```yml
url: jdbc:mysql://127.0.0.1:3306/guns?autoReconnect=true&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&serverTimezone=Asia/Shanghai
```
修改:
把serverTimezone=UTC改为serverTimezone=Asia/Shanghai
UTC为世界统一时间，可以选择东8区的Hongkong、Asia/Shanghai或者Asia/Hongkong作为参数


原网址 [SpringBoot返回date日期格式化，解决返回为TIMESTAMP时间戳格式或8小时时间差](https://blog.csdn.net/beauxie/article/details/78552919)
在其他方案没有解决的情况下，包括[spring boot项目使用@JsonFormat无效问题](https://blog.csdn.net/luan666/article/details/80399349)

方法三：
```java
@JSONField(format = "yyyy-MM-dd HH:mm:ss")  //FastJson包使用注解
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone = "GMT+8") //Jackson包使用注解
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")   //格式化前台日期参数注解
    private Date createTime;
```

解决方案：在application.properties配置文件增加以下配置:
```yml
spring.jackson.date-format=yyyy-MM-dd HH:mm:ss
spring.jackson.time-zone=GMT+8
```
第一行指定date输出格式为yyyy-MM-dd HH:mm:ss；
第二行指定时区，解决8小时的时间差问题。

