---
layout:     post
title:      back quote
subtitle:   Golang back quotes  反引号 <br/>Golang double quotes 双引号
            
date:       2018-08-21
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - GO
    - Back Quote
---

```
str1 := "这里是一串文本\n\n第二行\n\n这里有一个<a href=\"http://www.baidu.com?order_sn=[:order_sn]\">链接</a>"
    
str2 := `这里是一串文本\n\n第二行\n\n这里有一个<a href=\"http:\/\/www.baidu.com?order_sn=[:order_sn]\">链接<\/a>`
```

* 分别对str1和str2做json处理，打印结果如下
```
str1 : 这里是一串文本\n\n第二行\n\n这里有一个\u003ca href=\"http://www.baidu.com?order_sn=[:order_sn]\"\u003e链接\u003c/a\u003e

str2 : 这里是一串文本\\n\\n第二行\\n\\n这里有一个\u003ca href=\\\"http:\\/\\/www.baidu.com?order_sn=[:order_sn]\\\"\u003e链接\u003c\\/a\u003e
```

* 看到区别了吧，双引号的string可以转义字符字面量，即将``\n``解释成为换行
* 反引号就不一样了，只会保留字面量，即理解为字面上写的是啥就是啥，你反引号里面写的是``\n``，那么打印出来就是``\n``这个字符，而不会转义成换行

![alt text](/img/quote.png "quote")
![alt text](/img/quote2.png "quote")


## 总结
* Go语言的字符串是一个用UTF-8编码的变宽字符序列，它的每一个字符都用一个或多个字节表示
即：一个Go语言字符串是一个任意字节的常量序列。

* Golang的双引号和反引号都可用于表示一个常量字符串，不同在于：
1、双引号用来创建可解析的字符串字面量(支持转义，但不能用来引用多行)
2、反引号用来创建原生的字符串字面量，这些字符串可能由多行组成(不支持任何转义序列)，原生的字符串字面量多用于书写多行消息、HTML以及正则表达式
3、而单引号则用于表示Golang的一个特殊类型：rune，类似其他语言的byte但又不完全一样，是指：码点字面量（Unicode code point），不做任何转义的原始内容。