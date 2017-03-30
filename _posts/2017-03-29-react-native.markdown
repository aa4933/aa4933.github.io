---
layout:     post
title:      "react-native大坑"
subtitle:   "sublime"
date:       2017-03-30 16:35:00
author:     "wuilly"
header-img: "img/post-bg-2016-07_09.jpg"
tags:
    - react-native
---
> 'About react'


## 关于react-native

由于`php`本身是服务器端语言的因素，我寻思学习一个能弯道超车的技术，由于安卓和ios发展已经近10年了

本身我如果从头学起的话，一来学了也没场景去使用，二来不够简单直接，操作难度大

所以就有了react-native

### 关于遇到的一些报错坑

* react native:Expected a component class,got [Object Object ]


``` php

这个问题很有意思，我写的时候按ide的默认去填写了标签，不符合jsx语法，导致了这个错误

<view>
</view>
改为大写
<View>
</View>
```

* rnpm-link ERR! It seems something went wrong while linking. Error: Cannot read property 'length' of 

``` php
这个问题是由于版本的问题引起的，在我使用0.27版本的时候react-native还未存在link命令

所以采用了rnpm去解决第三方插件引入的问题，我引入ionicon的时候产生了一个问题，就是这个link的问题

我当时的解决方案是

不指定rnpm的版本，直接安装rnpm最新的版本解决问题

但是后续还是没办法使用ionicon这个第三方库，产生了下面的新问题
```

* Unrecognized font family ionicons

``` php
这个问题我忙了很久

打开ios目录下的xcode文件，add进去tff，然后填写info.plist------未解决

重新安装，重新启动编译---未解决

原以为是ionicon的问题，手动配置使用了阿里的iconfont------未解决

后来换了0.42的react-native，react-native link xxx + react-native link ------解决问题

重新手动配置iconfont -----成功

虽然解决了这个问题，但是我依然不知道原理为什么，

只是我最早使用0.27版本的时候玩坏了一个npm编译时候自动运行node的服务命令行

然后导致每次都要npm start，所以就重载了一个

凭借bing和google的经验来判读大概可能哪里有问题

所以我又产生了下面的新的问题

``` 

* react-native Netword request failed 

``` php

我在0.27的时候，rap上面mock测试数据的图片是可以使用的，但是，一到了0.42所有图片都无法使用

我驱动了http://localhost:8081/debugger-ui

来检测,结果配置了大半天的info，忽略他的http限制，结果就是不行

然后我加上了一个 react-native upgrade

竟然好了。。。但是很不幸的是，又回到了上面的问题，找不到ionicons

接下来我毫无办法，只能用一个解决方案了

重新init了一个，不使用ionicons，我怀疑可能是react-native link 本身存在问题，影响了一些配置

因为我0.27版本的时候也是这个问题，异常诡异，咱们还是不要用ionicons把，用阿里的iconfont吧，简单，自己配置容易掌控

默认的link配置info.plist我真的不知道怎么解决报错，第一次感觉坑那么大。。。

```




