---
layout:     post
title:      "影响mysql性能的因素-入门篇"
subtitle:   "Mysql"
date:       2017-04-21 10:52:00
author:     "wuilly"
header-img: "http://ww2.sinaimg.cn/large/006tNbRwgy1ffqse5ri9gj31hc0u0e3j.jpg"
tags:
    - MysqlLearning
---
> 'About more'

# 优化金字塔

![](http://ww2.sinaimg.cn/large/006tKfTcgy1fm4uy56kqoj30vi0gawga.jpg)

## 前言铺垫

基本的基础

### 专业名次解释

- QPS:每秒查询数量
- TPS:每秒执行数量
- 并发数量:同一时间处理的请求的数量
- 同时连接数量:同时连接的数量---可以是sleep状态
- cpu使用率:顾名思义
- 磁盘IO:磁盘读写

> 大量的并发会使数据库连接数量被占满，在mysql中默认的max_connections是100，所以一般如果不进行修改,benchmark会对一般网站造成极大的影响

网卡流量也是影响很大的，一般我们通过以下途径避免无法链接数据库

1. 减少从服务器的数量
1. 进行分级缓存，防止客户端缓存全部被破坏，造成大量查询
1. 避免`select *`
1. 分离业务网络与服务器网络


### 大表带来的影响

> 一般当一个表数据量超过千万，大小约为10g的时候，会带来很大的影响

* 大表对于DDL的影响
    
- [x]     建立索引需要很长时间
- [x]     mysql<5.5建立索引会锁表
- [x]     mysql>5.5虽然不会锁表，但是会引起主从延迟

* 如何解决数据库的大表

1.     分库分表----实际操作难度很大，非常影响前后端业务，非常不建议
1.     大表历史记录归档----归档时间点选择很重要
    

### 大事务带来的影响

> 事务是数据库系统区别普通文件存储最关键的属性，可简单理解为sql

* 事务有4个特点，这里就不像课本里面一样列举了

四个特点其中两个主要是说明事务执行的持续，不会部分完成，要么全部失败

要么全部成功，不会破坏完整性

另外两个一个是隔离性，不同的事务执行完毕以后相互的影响，未提交以前不

可见，mysql默认的innodb隔离级别是`可重复读`

``` php
show variables like '%ios%';查看隔离级别
事务开始
begin;
select * from xxx
commit;
事务结束
```

![](http://ww1.sinaimg.cn/large/006tNc79gy1fgahdm3xaqj30wi0e0n0f.jpg)

另外一个特点是，只要保存了，硬盘不坏就没事


* 如何处理大事务
    
1.     避免一次处理太多数据
1.     移出不必要的`select`操作

### 基础知识——什么影响了性能

数据库设计对性能的影响

* 过分的范式化为表建立了太多的列
* 过分的范式化造成了太多表关联
* 在OLTP环境中使用不恰当的分区表
* 使用外键保证数据完整性（影响数据复杂度，但是关联键索引是必须的）

### 优化顺序

1. 数据库结构设计以及sql优化（随着变更反复持续进行）
2. 数据库存储引擎和参数配置
3. 系统选择以及优化
4. 硬件升级