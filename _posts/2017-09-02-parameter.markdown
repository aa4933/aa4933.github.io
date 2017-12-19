---
layout:     post
title:      "影响mysql性能的因素-参数篇"
subtitle:   "Mysql"
date:       2017-04-21 10:52:00
author:     "wuilly"
header-img: "http://ww1.sinaimg.cn/large/006tKfTcgy1fj5bviyytkj30qo0gojsu.jpg"
tags:
    - MysqlLearning
---
> 'About parameter'

### Mysql服务器参数配置

1. 首先要获取参数配置路径
    
    -  注意：不同系统下，读取信息顺序不一样
    
    ```php
    
    mysqld --help --verbose | grep -A 1 `Default options`
    
    ```
    
    -  注意：我们也可以通过指定顺序读取指定位置文件

2. mysql参数作用域
    
    * 全局参数
        
        ``` php
        set global 参数名 = 参数值;
        set @@global.参数名 := 参数值;
        ```
        
    * 会话参数
        
         ``` php
        set [session] 参数名 = 参数值;
        set @@session.参数名 := 参数值;
        ```
    - 注意：全局参数更改对全局有效，会话参数同名默认取全局参数，有的全局参数更改仅仅对新的会话session有效，已经连接会使用原来参数
    
    - 注意：说明我们每次配置完，都要退出，然后重新连接
    
    - 注意：部分参数，占用内存很大，这些参数建议在会话session级别修改

3. mysql配置项

### 内存相关

* 确定可以使用内存上限大小
* 确定Mysql的每个连接使用的内存
    - [x] 需要注意的配置项（全都是为了某个线程占用的）
    - sort_buffer_size:每次连接排序都会占用设置的内存数量，连接如果过多，一瞬间上升的内存会非常高
    - join_buffer_size：连接缓冲区大小
    - read_buffer_size：全盘扫描时，读缓存的大小，一定是4k倍数
    - read_rnd_buffer_size:索引缓冲区大小
* 确定需要为操作系统保留多少内存
* 如何为缓存池分配内存
    - Innodb_buffer_pool_size(innodb缓存池大小)
        - [ ]  计算方法：总内存-(每个线程所需要的内存*连接数)-系统保留内存 约等于75% 如果innodb快速增长，需要增加容量
    - key_buffer_size
        - [ ]   注意：即使使用的是innodb，还是要为myisam预留一些空间      
        ``` php
            select sum (index_length) from information_schema.tables
            where engine = 'myisam'
        ``` 
        
### IO相关

* innodb io相关配置
    
    * innodb_log_file_size ： 和业务有关，看是否繁忙
    * innodb_log_files_in_group
            事务日志总大小 = 
            innodb_log_files_in_group
            * 
            innodb_log_file_size
    * innodb_log_buffer_size 32m-128m 足够
    * innodb_flush_log_at_trx_commit 建议为2
        ![](http://ww1.sinaimg.cn/large/006tKfTcgy1fj5ccigqnvj30sm090wir.jpg)
    * innodb_flush_method = O_DIRECT(在linux内核下)
    * innodb_file_per_table=1
    * innodb_doublewrite=1(增加数据安全性)

* myisam io相关配置（由于系统还采用此引擎，所以需要了解）

    * delay_key_write
![](http://ww3.sinaimg.cn/large/006tKfTcgy1fj5ch12rocj30ro07owhw.jpg)
off比较安全但是性能差
all如果发生奔溃，索引文件会损坏，需要myisam方式修复

### 安全相关

* expire_logs_days 指定清理binlog的天数
* max_allowed_packet 控制mysql可以接受包的大小（改大一点32m）主从一致
* skip_name_resolve 禁止dns查找（建议关闭）
* sysdate_is_now 确保sysdate()返回确定性日期
* read_only 禁止非super用户写权限
* skip_slave_start 禁用slave自动恢复
* sql_mode 设置mysql所使用的sql模式（谨慎，修改会导致部分sql无法使用）

### 其他相关

* sync_binlog 控制mysql如何向磁盘刷新binlog（参数大于0代表每次事务发生刷新次数建议为1）
* tmp_table_size和max_heap_table_size控制内存临时表大小
* max_connections 控制允许的最大连接数


## 总结

以上以上所有的硬件和系统和参数配置，加起来还比不过数据库结构设计和sql设计，所以一般情况下，我们会重点放在后面，而不是前面的参数和硬件和系统
