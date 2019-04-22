---
layout:     post
title:      PHP Curl Post Tmp Cert File
subtitle:   PHP Post Tmp Cert File Without Create File
date:       2019-03-21
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - PHP
    - Curl
    - Cert
    - Post
---

### [原文参考](http://www.enochzg.cn/2019/03/13/PHP%E5%A6%82%E4%BD%95%E6%94%AF%E6%8C%81CURL%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%AF%81%E4%B9%A6%E4%BC%A0%E8%BE%93.html?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)


## 前言

>最近在对接微信支付的时候，需要在退款处用到证书，由于我们是SAAS平台，要支持多方多渠道支付，
>如果把所有证书文件保存在应用服务器会受到SLB的影响，会导致某台机器文件不同步而阻碍退款流程，
>但把文件存在OSS的话，后端又要从OSS下载到应用服务器来保证一致性。思来想去，最终决定将证书内容保存在数据库，
>不同客户各对应一份证书文件，无论几台机器做集群都能保证文件的一致性，同时也避免了多余的下载步骤。



## 问题
>但是刚做就遇到了问题，PHP的CURL证书并不支持字符串的传输，只能填写证书路径（以下是官方的说法）

```
Client certificates must be specified by a path expression to a certificate store.
``` 


## 解决过程
>我第一个想到的就是创建空白文件，将证书内容写进去，等证书使用完毕后再将文件删除，但是创建实体文件再删除的操作消耗性能不说，还非常麻烦，有没有创建临时文件的方法呢？有，
>```tmpfile()```函数就可以帮我们创建临时文件并拿到文件路径，于是我写了一个获取临时文件路径的方法

```php
<?php 
public function getTmpPathByContent($content)
{
    $tmpFile = tmpfile();
    fwrite($tmpFile, $content);
    $tempPemPath = stream_get_meta_data($tmpFile);
    return $tempPemPath['uri'];	 ///tmp/phpXZCtAO
}
?>

```

* 比较悲哀的是，通过这个方法返回的路径根本读不到内容，甚至一度以为是不是被骗了

```
file_get_contents(/tmp/phpyyiOZv): failed to open stream: No such file or directory

```

>看了官方文档才找到原因，如果```tmpfile()```返回的句柄引用计数为0的话就会将临时文件回收，
>临时路径自然也就失效了，显然方法```getTmpPathByContent()```执行完后，
局部变量```$tmpFile```的生命周期就结束了（官方文档如下）

```
The file is automatically removed when closed (for example, by calling fclose(), 
or when there are no remaining references to the file handle returned by tmpfile()), 
or when the script ends.

```


>确认了根源，那我们现在亟需找到一个生命周期随进程结束而终止的变量类型来保存句柄，
什么类型能满足条件呢？静态变量。静态变量与局部变量不同的是，
在PHP生命周期开始时便会为其分配内存空间，并会把它存储在全局变量区域，而全局变量是在模块关闭阶段销毁的，
这样的话，声明静态变量就可以使```$tmpFile```引用计数持续保持大于0的状态，那我们的代码就可以做出如下处理
 

```php

<?php
    public function getTmpPathByContent($content)
    {
        static $tmpFile = null;
        $tmpFile = tmpfile();
        fwrite($tmpFile, $content);
        $tempPemPath = stream_get_meta_data($tmpFile);
        return $tempPemPath['uri'];
    }
?>

```

* 再执行一次就成功读取了临时文件的内容

```
-----BEGIN CERTIFICATE-----
MIIEbDCCA9WgAwIBAgIEAWJKHDANBgkqhkiG9w0BAQUFADCBijELMAkGA1UEBhMC
Q04xEjAQBgNVBAgTCUd1YW5nZG9uZzERMA8GA1UEBxMIU2hlbnpoZW4xEDAOBgNV
BAoTB1RlbmNlbnQxDDAKBgNVBAsTA1dYRzETMBEGA1UEAxMKTW1wYXltY2hDQTEf
MB0GCSqGSIb3DQEJARYQbW1wYXltY2hAdGVuY2VudDAeFw0xNzA4MDcwOTIxNDda
Fw0yNzA4MDUwOTIxNDdaMIGbMQswCQYDVQQGEwJDTjESMBAGA1UECBMJR3Vhbmdk
b25nMREwDwYDVQQHEwhTaGVuemhlbjEQMA4GA1UEChMHVGVuY2VudDEOMAwGA1UE
CxMFTU1QYXkxMDAuBgNVBAMUJ+a3seWcs+W4guaYjua6kOi9r+S7tuiCoeS7veac
iemZkOWFrOWPuDERMA8GA1UEBBMIMTAyNTkyODEwggEiMA0GCSqGSIb3DQEBAQUA
A4IBDwAwggEKAoIBAQDg2D3++uOxY/yMGQPBnROvyYimnCsfGE0dnqdGUTCykqBh
yfv82zE1/St/4DQX2QDiIvLif+sMGcYwF4bkzdY+HgitYLI0k5o/5LCNZOMctuio
kdYC2bNdWHq2y9S5UWLQR1Zvq+6QyPBVBVY9yq9xtQhIlUTsZnICAp3iQLfQUR3l
aEdH9IERoRUIkbyb8oX5ONQz4P9jOeE9C5iwx0QrH4s01NFhkhr8JHlugRLpo9vA
xGgi/48fOlONj6wWal5Gt0OvvEbIwgQwya15KBX2YeGnZvYBQa+lQMeXEqZSFie3
G+wGvbtlONczQEtp+JDxLZLUS/FT7U0TQN/t8JDvAgMBAAGjggFGMIIBQjAJBgNV
HRMEAjAAMCwGCWCGSAGG+EIBDQQfFh0iQ0VTLUNBIEdlbmVyYXRlIENlcnRpZmlj
YXRlIjAdBgNVHQ4EFgQUjDJ75bu3Roog7XOH6uFAdZ6kpcIwgb8GA1UdIwSBtzCB
tIAUPgUm9iJitBVbiM1kfrDUYqflhnShgZCkgY0wgYoxCzAJBgNVBAYTAkNOMRIw
EAYDVQQIEwlHdWFuZ2RvbmcxETAPBgNVBAcTCFNoZW56aGVuMRAwDgYDVQQKEwdU
ZW5jZW50MQwwCgYDVQQLEwNXWEcxEzARBgNVBAMTCk1tcGF5bWNoQ0ExHzAdBgkq
hkiG9w0BCQEWEG1tcGF5bWNoQHRlbmNlbnSCCQC7VJcrvADoVzAOBgNVHQ8BAf8E
BAMCBsAwFgYDVR0lAQH/BAwwCgYIKwYBBQUHAwIwDQYJKoZIhvcNAQEFBQADgYEA
ucJLJkkHxlqQCEapZOWmySutqNVZxFbqyG//UXxxpA/1yG4e+KmufKZWv+c+MtYI
8i0KDDCv/UE+kkFIrHYDDKsdLRpxrYOUHGoqq0c7yBJ6Dimgy6m8U8FsEv3HtUR2
8g5xrg2Tc5MPWEp9ncEw575hGk0CXLDGOkI1nU+pGqk
```


* 下面就可以把生成的临时文件地址设置到CURLOPT_SSLCERT了
 
 
 ```php
 <?php
     $sslCertPath = getTmpPathByContent($content);
     curl_setopt($ch,CURLOPT_SSLCERT, $sslCertPath);
     //......
 ?>
 
```