---
layout:     post
title:      "mysql分库分表"
subtitle:   "php"
date:       2018-01-24 11:52:00
author:     "wuilly"
header-img: "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1516777063737&di=a86a9881000f70190aaffe6953eec4f3&imgtype=0&src=http%3A%2F%2Fimg.article.pchome.net%2F00%2F28%2F07%2F58%2Fpic_lib%2Fwm%2F1920_1200car_1002.jpg"
tags:
    - Mysql
---
> 'About Mysql'


### Mysql 分库分表

一个实例的多个数据库

### 插播一个mysql的小坑

mysql 默认设置最近时间，不能出现两个及以上的timestamp

mysql timestamp有千年虫2038的坑／datetime不能设置默认的值（可以null）
