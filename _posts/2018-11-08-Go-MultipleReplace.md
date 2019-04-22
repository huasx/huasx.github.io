---
layout:     post
title:      Golang replace multiple
subtitle:   Golang replace multiple characters in string
date:       2018-11-08
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - GO
    - Replace
---
## 前言

>之前做过占位符的东西，在处理中需要进行字符串的匹配与替换，有时候会有很多占位符，一般的做法是for占位符循环去替换
>后来发现，可以换一种方式，就好像PHP里面的```str_ireplace``` 第一个参数是个array() 
>搜了一波stackoverflow发现了golang里面的一个NewReplacer工具，用法如下


```
	input := "/v1.0/emp/emp_1/my_stats"

	replacer := strings.NewReplacer("/emp_1/", "_", "/v1.0/", "")

	output := replacer.Replace(input)
	fmt.Println("Output:", output)

```

* 输出

```
Output: emp_my_stats

```

### 说明

```
NewReplacer returns a new Replacer from a list of old, new string  pairs. 
Replacements are performed in the order they appear in the target string, 
without overlapping matches.


```
* 这个方法需要先将替换的东西生成一个对象然后去调用替换
* 参数必须是复数，一个旧的紧跟一个新的