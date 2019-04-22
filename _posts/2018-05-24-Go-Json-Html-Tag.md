---
layout:     post
title:      Golang JSON编码时保留HTML标签
subtitle:   Golang json时保留HTML标签
date:       2018-05-24
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - GO
    - JSON
---
# 前言

>在使用go的json包进行json编码时，会将html标签的 ``<`` 转换成 ``\u003c``
>而最近在开发的微信客服消息推送就会出现以下结果
```
\u003ca href='https://www.example.com'\u003e点击进入\u003c/a\u003e
```
>其实仅仅是一个a标签而已

# 目的
**然而我们希望保留** ``<`` **时，需要使用json编码器的一个方法** ```SetEscapeHTML``` **方法，接收一个bool值来设置是否保留HTML标签。**

# 解决

**上代码**
```
func BuildJson(data map[string]interface{}) ([]byte, error) {
    buf := bytes.NewBufferString("")
    encoder := json.NewEncoder(buf)
	encoder.SetEscapeHTML(false)
	if err := encoder.Encode(&data); err != nil {
		return nil, err
	} else {
		return buf.Bytes(), nil
	}
}
```