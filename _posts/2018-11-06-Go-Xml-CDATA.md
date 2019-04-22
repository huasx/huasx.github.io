---
layout:     post
title:      Golang XML 添加CDATA
subtitle:   Golang XML 添加CDATA
date:       2018-11-06
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - GO
    - XML
    - CDATA
---
# 前言

>微信的推送都是xml的消息体，在我们同步响应微信消息的时候，也需要返回xml的格式，并且，由于我们响应的内容五花八门
>有时候就会带上```<br/>``` 之类的html字符，但是golang在编码成xml的时候会转义成实体字符
![alt text](/img/xml_cdata1.png "xml 编码")

##### google一圈之后，发现大家的做法都是增加一个字段叫做CDATA的结构体 

* 比如我之前想给结构体Text加CDATA

`````
type Text struct {
	Content string `json:"Content"`
}

`````

* 直接在后面加个json tag是无法实现的，除非自己实现了json那一套，能力有限就没有那么写
* 那么我就按照网上的方式重新定义了一下结构体

```
type Text struct {
	Content CDATA `xml:"Content" json:"Content"`
}


type MpText struct {
	Content string `json:"Content"`
}


type CDATA struct {
    Value string `xml:",cdata"`
}


```

* 先将json格式的数据 解析到MpText的结构体里面，这是完全没有问题的
* 然后将MpText.Content赋值给Text的CDATA.Value
* 这里的CDATA的xml tag一定要是小写，我大写的话会出现下图1
* 逗号也不能省略，否则会出现下图2
* 最终实现之后如图3

```xml
<xml>
    <ToUserName>
        <![CDATA[oIawIj6HjtMakoO42ghbHJeAIBEY]]>
    </ToUserName>
    <FromUserName>
        <![CDATA[gh_ba43c0866a80]]>
    </FromUserName>
    <CreateTime>1541489253</CreateTime>
    <MsgType>
        <![CDATA[text]]>
    </MsgType>
    <Content>
        <![CDATA[1111<br />2222<br />3333]]>
    </Content>
</xml>

```

![alt text](/img/cdata2.jpg "图1")
![alt text](/img/cdata3.jpg "图2")
![alt text](/img/cdata4.png "图3")


