---
layout:     post
title:      Golang net/url
subtitle:   Golang标准库http/url Values
            
date:       2019-02-22
author:     Ricky
header-img: img/5780743b45040.jpg
catalog: true
tags:
    - Golang
    - net/url
---

## 前言
>Golang标准库http/url Values

>这个库里面 有两个方法非常类似，用的时候也比较模糊，虽然没出什么大问题，但是，golang作为一个高性能语言，怎么可能出现两个功能相同的方法呢？
于是乎，简单的测试了一下，总算搞清楚了
>分别是Add方法和Set方法


```Golang

    package main
    
    import (
    	_ "app/core/testutil"
    	"log"
    	"net/url"
    )
    
    func main() {
    	data := url.Values{}
    	data.Add("qkey", "150032-SH1672384")
    	data.Add("qdate", "2019-01-14")
    	log.Println(data)
    	// 先用Add方法 打印结果
    	data.Add("qdate", "33333")
    	log.Println(data)
    }

//打印结果
map[qkey:[150032-SH1672384] qdate:[2019-01-14]]
map[qkey:[150032-SH1672384] qdate:[2019-01-14 33333]]

```

>意思就是追加与原有的key后面呗：Set方法将key对应的值集设为只有value，它会替换掉已有的值集。

```Golang

    package main
    
    import (
    	_ "app/core/testutil"
    	"log"
    	"net/url"
    )
    
    func main() {
    	data := url.Values{}
    	data.Add("qkey", "150032-SH1672384")
    	data.Add("qdate", "2019-01-14")
    	log.Println(data)
    	
    	// 换成Set方法再试
    	data.Set("qdate", "33333")
    	log.Println(data)
    }



//打印结果
map[qkey:[150032-SH1672384] qdate:[2019-01-14]]
map[qkey:[150032-SH1672384] qdate:[33333]]

```
>意思就是覆盖原有key的值呗：Add将value添加到key关联的值集里原有的值的后面。
