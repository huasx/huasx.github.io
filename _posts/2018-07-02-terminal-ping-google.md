---
layout:     post
title:      Terminal Ping Google
subtitle:   Mac OS Terminal Ping Google
date:       2018-07-02
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - Mac
    - Ping Httping
---



### [原文参考](https://segmentfault.com/q/1010000006634279/a-1020000007276629)

* 一直被一个问题困扰着，就是浏览器可以翻墙，并且 **终端也可以访问Google**，但是问题就是，终端无法ping的通Google

![alt text](/img/ping1.jpeg "ping 不通google")


* 网上搜了一圈之后，原来ss代理是基于tcp或者udp协议，而ping是走的icmp协议因此在ss下不能ping通google
* 那如果非得要ping的通呢，这里介绍一个工具 ``httping``

```
测试结果：本来我直接ping谷歌是不行的，但是httping之后，ping也可以可能是dns缓存，但是清理缓存也还是能够ping通,原因待查

```


### Mac安装
```
 brew install httping
```

### 查看
```
 httping --help
```

* -x 使用代理 host:port 
* -g ping host


### 使用
```
 httping -x 127.0.0.1:1087 -g google.com
```
![alt text](/img/ping2.jpeg "ping 的通google")

* 原文介绍 httping之后，直接ping也能通，但是自己测试还是ping不通