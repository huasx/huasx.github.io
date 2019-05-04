---
layout:     post
title:      CGI FAST-CGI PHP-FPM 
subtitle:   CGI、FastCGI和php-fpm的概念和区别
            
date:       2019-05-04
author:     Ricky
header-img: img/5780743b45040.jpg
catalog: true
tags:
    - PHP
    - CGI
    - PHP-FPM
---

### 概念

#### CGI
> CGI（Common Gateway Interface）全称是“通用网关接口”，WEB 服务器与PHP应用进行“交谈”的一种工具，其程序须运行在网络服务器上。CGI可以用任何一种语言编写，只要这种语言具有标准输入、输出和环境变量。如php、perl、tcl等。
> CGI: 通俗的讲，cgi是为了保证web server传递过来的数据是标准格式的，也就是定义协议
> Web Server仅仅是内容的分发者。CGI就是规定要传哪些数据、以什么样的格式传递给后方处理这个请求的协议。

#### FastCGI介绍
> FAST CGI 简单说就是CGI的升级版本，性能更优
> php-fpm是一个实现了fast cgi的程序
> PHP的解释器是php-cgi,php-fpm管理的对象就是php-cgi

#### PHP-FPM介绍
> PHP-CGI就是PHP实现的自带的FastCGI管理器。 
> PHP-FPM 是对于 FastCGI 协议的具体实现，他负责管理一个进程池，来处理来自Web服务器的请求。目前，PHP5.3版本之后，PHP-FPM是内置于PHP的。
> PHP-FPM通过生成新的子进程可以实现php.ini修改后的平滑重启。

#### [原文参考](https://segmentfault.com/q/1010000008356979)
