# 《穿越火线》——纳塔丝 6 7 8

> 原文：<https://infosecwriteups.com/over-the-wire-natas-6-7-8-a90e3981fdd1?source=collection_archive---------2----------------------->

# 超过 6 级

![](img/2221e506fdecc2b8ed4ec64ddf12366e.png)

6 级

查看“查看源代码”链接

![](img/bd6f31167bc34cfed1220241367c1e93.png)

当前级别的 PHP 源代码

PHP 代码包含一个文件“includes/secret.inc”

然后将＄secret 变量与级别页面上输入文本字段中的 HTTP POST 数据“secret”参数进行比较

如果匹配，则打印 natas7 的密码

查看“/includes/secret.inc”

![](img/b2ba12e802fe8b08064a151ad635e1c6.png)

秘密公司

包含的文件创建了一个$secret 变量，它的值是获取 natas7 密码的秘密

在级别输入文本字段中输入密码，并获取 natas7 密码

![](img/95162b6f831beb9be35eb3df59faa5d4.png)

# 越线 7 级

![](img/1a160d9dc26d49301f579cf9c785711d.png)

Natas7 水平

检查源页面和链接页面

![](img/d4edaadd552488da04ac7064bf25afab.png)

温柔的提醒所有的密码在哪里

![](img/2585332836bbe4a37a2774e256e76ffc.png)

主页链接

点击“首页”向我们展示了这个页面，网址已经变成了“index.php？page=home "

![](img/f977de85ff063f3ae211cb2f75cb1ddf.png)

包含错误

手动转到“index.php？page=nothere”向我们展示了 index.php 试图包含一个从我们传递的“page”参数中命名的文件，并且它抛出一个找不到文件的错误

![](img/8990c264e31dee95364945ee9d477960.png)

Natas8 密码

然后手动转到“index.php？page=/etc/natas_webpass/natas8 "向我们展示了下一关的密码

# 越线 8 级

![](img/1c90fb7275407a73288f7bfce5549986.png)

纳塔 10 水平

检查查看源代码链接

![](img/92cce55f681e96be65ccf09fef41167d.png)

级别页面源代码中的 PHP 代码片段

有一个 PHP 代码片段检查文本输入字段中的“secret”POST 数据参数是否与经过多次编码的“$encodedSecret”变量中的静态值相匹配:

返回 bin 2 hex(strrev(base64 _ encode($ secret))；

![](img/4ebd5c51444fa9faf5699a106b7438dd.png)

在谷歌上找到的 PHP 游乐场

通过使用在 Google-tehplayground.com 上找到的 PHP 操场，找到颠倒代码中操作的密码

```
print (base64_decode(strrev(hex2bin($encodedSecret))));
```

![](img/caf74fe88207c2aca6a803c9f4901aaa.png)

Natas9 密码

输入以前发现的秘密的纯文本，并获得 natas9 的密码

这就结束了 OTW·纳塔斯 6 7 8 级

我希望你喜欢它。

PVXs

[](https://twitter.com/pivixih) [## JavaScript 不可用。

### 编辑描述

twitter.com](https://twitter.com/pivixih) [](https://tryhackme.com/p/PVXs) [## TryHackMe | PVXs

### TryHackMe 是一个免费的学习网络安全的在线平台，使用动手练习和实验室，通过您的…

tryhackme.com](https://tryhackme.com/p/PVXs)