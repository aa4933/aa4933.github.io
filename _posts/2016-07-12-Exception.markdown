---
layout:     post
title:      "php各类异常的处理"
subtitle:   "小技巧总结文章"
date:       2016-07-12 12:00:00
author:     "wulihh"
header-img: "img/post-bg-2016-07_08.jpg"
tags:
    -php
---
> “Exception”


## 关于异常处理

### 前言
对于异常处理，一定不会陌生，但是如何能进行更好的异常处理，让异常得到良好的释放，而不是一大屏幕红字，影响速度又增加PHP内部的消耗，这是一个非常重要的课题
```php
//如果表中数据出现错误，会被异常捕捉，执行对应的异常报错
set_error_handler('myerror',E_NOTICE);
```

```php
try{}
catch{}
```

```php
if(){
}
```

以上3种是总结的最简单的异常处理的3种方法，几乎可以解决百分之90的异常处理，以下是错误级别与情况


| 值	 | 常量   | 描述  |
| --- |:--------:| -----|
| 2	| E_WARNING | 非致命的 run-time 错误。不暂停脚本执行。  |
| 8 | 	E_NOTICE |   Run-time 通知。脚本发现可能有错误发生，但也可能在脚本正常运行时发生。 |
| 256 | E_USER_ERROR | 致命的用户生成的错误。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_ERROR。 |
| 512 | E_USER_WARNING|非致命的用户生成的警告。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_WARNING。|
| 1024 |E_USER_NOTICE|用户生成的通知。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_NOTICE。|
| 4096 | E_RECOVERABLE_ERROR|可捕获的致命错误。类似 E_ERROR，但可被用户定义的处理程序捕获。(参见 set_error_handler())|
| 8191 | E_ALL | 所有错误和警告，除级别 E_STRICT 以外。（在 PHP 6.0，E_STRICT 是 E_ALL 的一部分）|


### 关于异常处理

#### 一、PHP错误处理的三种方式
 * A、最简单的if处理，这里就不加赘述了
 
 * B、自定义错误和错误触发器
	 * 1、错误处理器（自定义错误，一般用于语法错误处理）
        创建自定义错误函数（处理器）
        语法：
     * 2、错误触发器（一般用于处理逻辑上的错误）注：个人并未使用到，可能是使用场景没到，所以感觉有点鸡肋
         [错误触发器代码](http://www.cnblogs.com/cnbeir/archive/2012/05/05/2484635.html)
         
```php
错误处理器
        function error_function($error_level,$error_message,$error_file,$error_line,$error_context)
        //创建好后还需要改写set_error_handler();函数
        set_error_handler('error_function',E_WARNING);
        //这里error_function对应上面创建的自定义处理器名，第二个参数为使用自定义错误处理器的错误级别；
```
	

* C、错误日志 
 
 默认的根据php.ini中error_log配置，php向服务器的错误记录系统或文件发送错误记录。
 通过使用error_log()函数可以向文件或远程目的地发送错误记录;
        语法：
        
```php
error_log(error[,type,destination,headers])
```

type部分一般用3，
表示在文件后面追加错误信息，而不会覆盖原内容
destination表示目的地，即存放的文件或远程目的地
 如：
 
```php
error_log("$error_info",3,"errors.txt");
```
####二、PHP异常处理【重点】
 
* 1、基本语法

```php
        try{
            //可能出现错误或异常的代码
            //catch 捕获  Exception是php已定义好的异常类
        } catch(Exception $e){
            //对异常处理，方法：
                //1、自己处理
                //2、不处理，将其再次抛出
        }
```

* 2、处理处理程序应当包括：
	
	 * A.Try - 使用异常的函数应该位于 "try"  代码块内。如果没有触发异常，则代码将照常继续执行。但是如果异常被触发，会抛出一个异常。
	 * B.Throw - 这里规定如何触发异常。每一个 "throw" 必须对应至少一个 "catch"
	 * C.Catch - "catch" 代码块会捕获异常，并创建一个包含异常信息的对象 

###后记

关于异常处理的两篇博文，写得非常好，我这边也借鉴了他们的文章，根据自己的需求提取了一些这次有使用到的异常处理

[PHP中的错误处理、异常处理机制](http://www.cnblogs.com/cnbeir/archive/2012/05/05/2484635.html)

[php错误及异常捕捉](http://blog.csdn.net/e421083458/article/details/8157154)
 

