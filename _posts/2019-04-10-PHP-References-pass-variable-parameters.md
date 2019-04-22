---
layout:     post
title:      PHP 引用传递可变参数 
subtitle:   如何通过PHP中的引用传递可变参数的参数？
date:       2019-04-1-
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - PHP
    - Args
    - 指针
---


## 前言

>PHP有一个可变参数的传参方式，但是，如果是这种方式接受的参数，其实是拷贝了一份，无法做到传指针也就是PHP里面的地址
>PHP里面无法打印变量指针（目前我没有找到方法打印）
>可变参数传参过去，如何做到传递地址呢？


### 先上一个列子，如果传递地址

```php
    
    <?php
    
    $fn1 = function (&$a, &$b) {
        $a = '2';
        $b = '3';
    };
    
    
    $a = '4';
    $b = '4';
    
    echo 'a:' . $a . PHP_EOL;
    echo 'b:' . $b . PHP_EOL;
    $fn1($a, $b);
    echo '-------------------------------' . PHP_EOL;
    echo 'a:' . $a . PHP_EOL;
    echo 'b:' . $b . PHP_EOL;

```
 
 
> 很简单，```$fn1``` 接受的是变量指针，会改变外部参数的值，所以最后打印出来如下：


```
    a:4
    b:4
    -------------------------------
    a:2
    b:3

```


> 那么，如果我不清楚 ```$fn1``` 到底有多少个参数呢？我依然想要去传递地址，又该如何？首先肯定会想到PHP的可变参数传参



```php
    $a = '4';
    $b = '4';
    
    $fn2 = function (...$arg) {
        var_dump($arg);
    };
    
    $fn2($a, $b);

    //输出如下：
    array(2) {
      [0] =>
      string(1) "4"
      [1] =>
      string(1) "4"
    }

```

> 但是并没有办法去传递地址，一个函数需要接受的是地址，是在定义的地方就写好的，而可变参数前面的```...``` 没有办法加```&```符号，
> 并且，```...``` 方式会拷贝参数组成一个数组


### 解决如下


```php

    <?php
    
    $fn1 = function (...$args) {
        $args[0]['a'] = 'a被改变了';
        $args[0]['b'] = 'b被改变了';
    };
    
    
    $a = '4';
    $b = '4';
    
    echo 'a:' . $a . PHP_EOL;
    echo 'b:' . $b . PHP_EOL;
    $fn1(['a' => &$a, 'b' => &$b]);
    echo '-------------------------------' . PHP_EOL;
    echo 'a:' . $a . PHP_EOL;
    echo 'b:' . $b . PHP_EOL;
    
    //输出如下
    a:4
    b:4
    -------------------------------
    a:a被改变了
    b:b被改变了
    
```

> ```...```会拷贝入参的数组，但是数组的元素是引用地址，那么拷贝之后的数组元素依然是取得地址，从而可以改变外部的变量

#### [原文参考](https://codeday.me/bug/20181114/375519.html)
#### [PHP文档参考](https://www.php.net/manual/zh/functions.arguments.php)