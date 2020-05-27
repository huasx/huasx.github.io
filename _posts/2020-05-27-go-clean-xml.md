---
layout:     post
title:      Go Clean Xml 
subtitle:   Cleaning XML Data Before Unmarshaling  in Go
date:       2020-05-27
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - Golang
    - xml
---


每个人都喜欢解析 XML 文件，对吧？ 格式不一致，没有文档化的字段和属性，它们总是很有趣。
如果你特别不幸，你还会遇到无效的 Unicode 字符，这会导致解析器阻塞，就像我最近做的那样:
```shell script
XML syntax error on line 12: illegal character code U+0014
```

网上搜罗了一圈，先上代码，看下如何解决
```golang
bt := []byte(strings.Map(func(r rune) rune {
    if unicode.IsPrint(r) {
        return r
    }
    return -1

}, string(body)))

mb := &msg.struct{}

err := xml.Unmarshal(bt, mb) // nil
```

看起来他只是把unicode中的不可打印字符替换了。
rune=-1 应该对应的是unicode中空字符的编码。
unicode中有的是可见字符，有的是不可见字符，他这段函数是替换掉了不可打印的unicode字符。
只有可见xml也是解析的unicode，unicode有不可见字符，会造成解析失败。
```\uffff-ffff``` 应该都是特殊字符 

也就是说，那段代码的含义其实就是替换了原本的unicode字符中的会干扰xml转码的字符为别的字符。

### [原文参考](https://blog.zikes.me/post/cleaning-xml-files-before-unmarshaling-in-go/)
