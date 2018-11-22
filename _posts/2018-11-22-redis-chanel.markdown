---
layout:     post
title:      "redis订阅与发布的实际应用"
subtitle:   "redis"
date:       2018-11-22 15:28:00
author:     "wuilly"
header-img: "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1516777063737&di=a86a9881000f70190aaffe6953eec4f3&imgtype=0&src=http%3A%2F%2Fimg.article.pchome.net%2F00%2F28%2F07%2F58%2Fpic_lib%2Fwm%2F1920_1200car_1002.jpg"
tags:
    - redis
---
> 'About redis'


### 应用场景

> 我一直希望在实际应用场景里面使用到swoole这项技术，在2015年的时候就对这个技术有所了解，当时的理解比较浅薄

> 今天为了实现一个实时通讯的订阅业务，有幸用上了这门技术

![image](https://wx2.sinaimg.cn/large/005N5norgy1fxgwo5encjj30kx0baabz.jpg)


> 以前使用过，也运用过该技术流程，不过之前是使用node去完成需求，这次用了swoole解决了我当时没有解决的消息传递的问题，之前只是简单的消息打招呼，并不知道传了什么东西，当时也没有特别去深究node，所以就这么实现了，这次的实现方案更加精准，传递了消息更加完美

#### 监听与订阅

> 同理这次使用得方式也是redis的订阅与发布

> 这次的swoole使用的是官方教案，虽然简单，但是确实好用

``` php

$server = new swoole_websocket_server("0.0.0.0", 9501);

$server->on('workerStart', function ($server, $workerId) {
    //$options['password'] = 'root';//这个地方设置redis密码，坑了我不少时间
    $client = new swoole_redis($options);
    $client->on('message', function (swoole_redis $client, $result) use ($server) {
        if ($result[0] == 'message') {
            foreach($server->connections as $fd) {
                $server->push($fd, $result[2]);//$result[1]代表频道名字
            }
        }
    });
    $client->connect('127.0.0.1', 6379, function (swoole_redis $client, $result) {
        $client->subscribe('msg_0');
    });
});

$server->on('open', function ($server, $request) {

});

$server->on('message', function (swoole_websocket_server $server, $frame) {
    $server->push($frame->fd, "hello");
});

$server->on('close', function ($serv, $fd) {

});

$server->start();


//下面是laravel的门面redis方法

        Redis::publish('msg_0', 'wuhang');
```


> 注意：redis在使用redis-cli连接的时候，service是无法断开并且销毁进程的，只有先断开才能完成重启配置


> swoole安装的时候会有一些资源同时需要安装


> 顺便写一下，经常用到的linux命令

```
telnet 127.0.0.1 80 //这个工具可以判断端口是否正常

netstat -npl 查看所有占用端口

vim 下的命令

/ 往下找 n 下一个

? 往上找 n 下一个

命令 & 长期挂载
```