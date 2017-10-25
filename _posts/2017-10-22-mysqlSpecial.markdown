---
layout:     post
title:      "禁止sql存储过程"
subtitle:   "Mysql"
date:       2017-10-22 10:52:00
author:     "wuilly"
header-img: "http://ww4.sinaimg.cn/large/006tKfTcgy1fj6elgidgej30hm0b9jsd.jpg"
tags:
    - machine leaning
---
> 'About mysql'

## mysql特殊能力

* 存储过程
    - 触发器
* 事务
* 视图

## 替代方案


存储过程使用后端脚本替代

触发器使用事务替代


---

## 原因

 1. 基于 DBA  的数据库逻辑很容易被忽略
 2. mysql触发器本身有`after delete`无法链式反应的问题
 3. 而且没有移植性