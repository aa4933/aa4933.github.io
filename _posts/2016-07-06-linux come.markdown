---
layout:     post
title:      "linux+php小技巧"
subtitle:   "小技巧总结文章"
date:       2016-07-04 12:00:00
author:     "wulihh"
header-img: "img/post-bg-2016-07_05.jpg"
tags:
    - Linux
---
> “sure It is the really php ”


## linux与php结合的小技巧


ubuntu 重启samba服务器

``` php
sudo /etc/init.d/smbd restart
```
在linux下快速找到某个确定的文件

``` php
sudo find / -name 'php.ini'  
```
快速找到php.ini

``` php
php -i |grep php.ini
```
在linux上直接查找php拓展

``` php
php -m
```
关于删除linux vi命令
4，删除所有内容：先用G 转到文件尾，然后使用下面命令：

``` bash
:1, .d
```
   删除第10行到第20行的内容：先用20G转到第20行，然后使用下面命令：

``` bash
:9, .d
```
关于删除的一些说明：

* 1 在vi中，" .  "表示当前行，“1，。”表示从第一行到当前行，“d ” 表示删除。
* 2 如果只是想删除某一行，那么把光标指到该行，然后输入d d 即可。

## 想说其他点东西：
* 任何事情都是有好有坏的，不可能是完美的 *

特别是最近遇到的事情，我觉得更加明显了

首先数据转移中XSS攻击的防范，你说这些数据肯定不能转移是吧，正好会导致sql语句出错，转移不过去，但是

我们如果转移的时候追求速度，必须要同时插入1W+或者更多的数据，那么XSS的数据就会导致整个SQL出错，那你说我们得过滤一下，你会发现，如果过滤了的话，这些数据也就一起转移走了

所以

### 转移==过滤XSS==无用数据转移

### 不转移==无法执行sql==无法提升效率

> 这就是所谓的万物都有好有坏