---
layout:     post
title:      "Docker+Laravel带来的小麻烦"
subtitle:   "php"
date:       2018-01-24 11:52:00
author:     "wuilly"
header-img: "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1516777063737&di=a86a9881000f70190aaffe6953eec4f3&imgtype=0&src=http%3A%2F%2Fimg.article.pchome.net%2F00%2F28%2F07%2F58%2Fpic_lib%2Fwm%2F1920_1200car_1002.jpg"
tags:
    - Docker
---
> 'About laravel'


### 前言

> 在使用Laravel过程中遇到了一些问题

### 来源

在编写一个Web程序的时候，笔者使用了Laravel大部分自带的功能，但是使用

的同时，遇到了一些问题，这些功能由于使用的人较少，市面上的解决方案较

少，笔者根据自己对系统与应用的理解，做了一些解决方案，记录问题探索的

过程。

* 即时通讯问题
* Laravel自带的上传存储功能


### 结果与过程

* 解决即时通讯的问题

最初我的思路是使用Swoole来进行通讯传输，后来我发现把PHP常驻内存的权

限托管与Nginx依附的开发方式有很大的区别，故选用资源较多的node作为客

户端中转站

![](http://ww3.sinaimg.cn/large/006tKfTcgy1fnrkrzq24hj314a0lk77n.jpg)

画图是非常重要的一个解决思路

Redis的消息队列作为不同语言的通讯中转站。


* 使用Laravel自带的上传功能

![](http://ww4.sinaimg.cn/large/006tKfTcgy1fnrkxokvwlj30ws0g0ado.jpg)

文档是着么写的，但是笔者照着做了以后，发现URL一直无法访问图片

经过了一天的折腾，终于找到了病根所在

> 环境：MAC 运行环境：Docker 

根本原因是MAC的软链接和Linux软链接有本质的区别，导致mac上链接了，但

是实际中Nginx服务器读取不出来

最后我进入docker命令行，解决了这个问题

熟悉的结果

![](http://ww2.sinaimg.cn/large/006tKfTcgy1fnrl0m4jogj31kw0qikjl.jpg)

