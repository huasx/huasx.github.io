---
layout:     post
title:      Count File Num
subtitle:   php count dir file num
            
date:       2018-12-13
author:     Ricky
header-img: img/5780743b45040.jpg
catalog: true
tags:
    - PHP
    - Dir Files
---

```
在进行文件打包的时候，需要对文件数量校验，之前都是一致用的readdir发现性能比较低，每次都需要先open文件，最后还需要close
今天发现一个glob函数，扫描当前目录下的文件,不包括文件夹,返回的是一个数组
```


```php
function getFileNumber($path)
{
    $num = 0;
    $arr = glob($path);
    foreach ($arr as $v) {
        if (is_file($v)) {
            $num++;
        } else {
            $num += getFileNumber($v . "/*");
        }
    }
    return $num;
}

```


* 优化一下如下，如果能确定文件的后缀，可以匹配出来

```
count(glob($path. '/*.png'));

```
