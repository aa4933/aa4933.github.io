---
layout:     post
title:      "多重匹配复合迁移"
subtitle:   "小技巧总结文章"
date:       2016-07-08 12:00:00
author:     "wulihh"
header-img: "img/post-bg-2016-07_09.jpg"
tags:
    - PHP
---
> “Make complaints”


## 多表数据定位---复合迁移,多重匹配

### 前言

在数据迁移过程中，我们会遇到一种数据库设计失误的情况，之前考虑不周到引起的，旧表数据字段没有存储关联子表的主键
导致无法快速定位数据的情况，我采用了一种叫做复合迁移多重匹配的方法进行定位

``` mysql
旧claim+user+app_version+app_project=新app_claim
```

### 关于表的细节

![Alt text](https://aa4933.github.io/img/post_other_img/post_07_13.png)

####分析

首先这张表有3个字段是无法直接转移的
* user_name-----这个字段可以通过uid进行2次获取
* game_id--------这个字段可以通过唯一字段package_name进行获取
* version_code--这个字段关联的app_version表中唯一能够定位到某一条记录的只有主键id，但是id并未存储在claim表中

所以我们使用了以下方式进行快速定位

![Alt text](https://aa4933.github.io/img/post_other_img/post_07_13_2.png)

复合迁移的方法很棒，但是我们迁移的时候，为了增加速度，使用的是mysql IN的方法进行匹配查询

这个时候，我们如果采用之前的思路，单匹配的方式肯定是不行的，故我采用了一种多重匹配的方法

#### 代码

main--run方法

``` php
//由于此表转移需要获取app_project表的多条信息
list($res_g,$game_ids)=self::get_old_other_Data_2($this->db_m,'app_project','package_name,id','('.$name.')');
//切割特殊的game_ids
$game_id=substr(arr_to_str($game_ids),0,-1);
//接下来需要通过game_id与version去同时匹配得到version_name
$res_v=self::get_old_other_Data_3($this->db_m,'app_version','pid,version_name,version_code','('.$game_id.')','('.$version.')');
//获取sql
$sql = $this->get_sql($res_c, $res_u,$res_g,$res_v);
```

get_old_other_Data_3方法

``` php
public function get_old_other_Data_3($db,$table,$information,$oldIDs,$version){
        $list=array();
        $sql = sprintf("SELECT %s FROM `%s` WHERE pid IN %s AND version_name IN %s;",$information,$table,$oldIDs,$version);
        $res=$db->prepare($sql);
        $res->execute();
        while($rows=$res->fetch()){
        //复合迁移
            $list[$rows['pid']][$rows['version_name']]=$rows;
        }
        return $list;
    }
```

get_sql方法

``` php
public function get_sql($res_c,$res_u,$res_g,$res_v){
        $sql='INSERT INTO
            app_claim
            VALUES';
        //如果表中数据出现错误，会被异常捕捉，执行对应的异常报错
        set_error_handler('myerror',E_NOTICE);
        foreach($res_c as $v)
        {
            //组装sql
            if ($res_c) {
                //将旧表信息插入新表中
                $sql = $sql.'(' . $v['id'] . ',' .$v['uid'] . ',
    "' .$res_u[$v['uid']]['my_username'].'",
    "' .$res_g[$v['package_name']]['id'].'",
    "' .$v['title'].'",
    "' .$v['package_name'].'",
    //多重匹配
    "' .$res_v[$res_g[$v['package_name']]['id']][$v['version']]['version_code'].'",
    "' .$v['version'].'",
    "' .$v['url'].'",
    "' .$v['state'].'",
    "' .$v['type'].'",
    "' .$v['failture'].'",
    "' .$v['desc'].'",
    "' .$v['remark'].'",
    "' .$v['create_time'].'")
    ';
                $sql=$sql.',';
            }
            else {
                echo "\n".'no data!'."\n";
                //失败的时候写入log文件
                Write_log($v['id']);
            }
        }
        //sql必须切割掉最后一个字符，否则会导致无法执行
        $sql=substr($sql,0,-1);
        return $sql;
    }
```

### 后记

>数据迁移过程中遇到的各种事情多而不绝，需要多总结，多思考，防止以后出现类似的错误
 

