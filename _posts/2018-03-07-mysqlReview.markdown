---
layout:     post
title:      "mysql重点优化细节-索引与sql"
subtitle:   "php"
date:       2018-01-24 11:52:00
author:     "wuilly"
header-img: "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1516777063737&di=a86a9881000f70190aaffe6953eec4f3&imgtype=0&src=http%3A%2F%2Fimg.article.pchome.net%2F00%2F28%2F07%2F58%2Fpic_lib%2Fwm%2F1920_1200car_1002.jpg"
tags:
    - Mysql
---
> 'About Mysql'


### Mysql结构设计

1. 


---


### Mysql索引优化

- [ ] 减少存储引擎扫描数据量
- [ ] 帮助排序避免临时表
- [ ] 随机IO变为顺序IO

> Btree索引

适用于：值匹配／值范围／范围／前半部分／最左前缀匹配查找

原理：B+树，树状切割分离顺序数据范围，指针连接，顺序连接

限制：非最左列／not in与<>／无法跳过索引中列（三复合索引情况）  

> HASH索引

适用于：精确匹配／字段唯一性较强（身份证）／不适合重复率较大字段（性别）

原理：HASH计算，唯一性

限制：无法排序／必须二次查找／HASH冲突

> 太多索引问题

增加写成本／增加查询优化器选择时间

> 索引策略

不能使用表达式与计算函数

**联合索引**

最左策略
   * 经常被使用的列优先
   * 选择性高的列优先
   * 宽度小的列优先

**覆盖索引**

存储引擎根据查询结果，用索引进行覆盖

无法使用情况
   * 存储引擎不支持
   * 查询太多列，比如*
   * 使用双%like匹配查询
   
---

### 索引维护

- 筛选重复与冗余索引

主要是联合索引与单（主键）索引重复

工具库 percona-toolkit

``` php

安装 

brew install percona-toolkit

使用

pt-duplicate-key-checker --user=root --host=127.0.0.1:3307  --password=123456 --databases=db_sdk

```
- 查找未被使用过的索引


``` php
select object_schema,object_name,index_name,b.`TABLE_ROWS`
  from performance_schema.table_io_waits_summary_by_index_usage a
  JOIN information_schema.tables b ON
  a.`OBJECT_SCHEMA`=b.`TABLE_SCHEMA`AND
  a.`OBJECT_NAME`=b.`TABLE_NAME`
  WHERE index_name IS NOT NULL
  AND count_star = 0
  ORDER BY object_schema,object_name;
```

- 更新索引统计信息以及减少索引碎片

``` php 
analyze table table_name;

optimize table table_name;
```



---

### sql语句优化

### 找出慢查询语句

1. 用户反馈
2. 慢查询日志
3. 实时获取性能问题sql


### 慢查询日志

相关参数设置

![](http://ww4.sinaimg.cn/large/006tNc79gy1fpa0ud3sxnj30xe0gwdq8.jpg)

> 使用方法 set globa l `xx=on`

### 慢查询日志分析工具

![](http://ww2.sinaimg.cn/large/006tNc79gy1fpa0yyp425j30xu0i4qet.jpg) 



### 实时发现有性能问题sql

设定脚本定时执行下面sql

``` php 
SELECT id,`user`,`host`,DB,command,`time`,state,info FROM information_schema.PROCESSLIST WHERE TIME>=0
```

#### 查询缓存对sql性能影响

查询缓存会加锁
不建议在读写频繁的场景使用

####  确定查询各个阶段消耗的时间

performance_schema > 5.6/5.7(推荐)

1.打开记录
``` php
use performance_schema;

update setup_instruments set enabled='yes',timed='yes' where name like 'stage%' ;

update setup_consumers set enabled='yes' where name like 'events%';

select count(*) from sakila.film;//测试sql语句
```

2.查询记录结果
![](http://ww4.sinaimg.cn/large/006tNc79gy1fpb10j4282j30va0co7d6.jpg)

3.得到结果

profile < 5.5

![](http://ww2.sinaimg.cn/large/006tNc79gy1fpb0sx42irj30ti0dqads.jpg)

### 优化特定场景下单sql

场景：![](http://ww1.sinaimg.cn/large/006tNc79gy1fpb12x2ntbj30ee0dkaeh.jpg)

解决方案：![](http://ww3.sinaimg.cn/large/006tNc79gy1fpb140bvt5j30ve0gctil.jpg)

场景：修改大表的表结构

![](http://ww2.sinaimg.cn/large/006tNc79gy1fpb17l9ncpj30li0baq6e.jpg) 

解决方案1:利用主从服务器，先改从，切换，再改主

解决方案2（以前的方案）:![](http://ww3.sinaimg.cn/large/006tNc79gy1fpb1dmedq2j30p00aqady.jpg)

解决方案3（使用工具简化方案2）：![](http://ww3.sinaimg.cn/large/006tNc79gy1fpb1j2ry72j30vu0ayqa0.jpg)


#### 减少not in与<>使用，改写为左／右连接

![](http://ww3.sinaimg.cn/large/006tNc79gy1fpb1s93gg8j30vi0bmgr2.jpg)

#### 使用汇总表优化查询（提前统计好）

![](http://ww2.sinaimg.cn/large/006tNc79gy1fpb1v7v85dj30uw0dk7be.jpg)

