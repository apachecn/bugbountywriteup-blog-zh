# 像专业人士一样绕过速率限制！

> 原文：<https://infosecwriteups.com/bypassing-rate-limit-like-a-pro-5f3e40250d3c?source=collection_archive---------1----------------------->

## 你好，臭虫赏金猎人！

这是我的第二篇文章，希望你喜欢。在这篇文章中，我将尝试分享我所知道的绕过速率限制的方法。所以让我们开始吧。

> **绕过带表头的速率限制**

有一些报头可以用来绕过速率限制。您所要做的就是在请求中使用主机标题下的标题。

1.  X-Forwarded-For : IP
2.  x 转发主机:IP
3.  x-客户端-IP : IP
4.  x 远程 IP : IP
5.  x 远程地址:IP
6.  x 主机:IP

*   每当请求再次被阻止时，更改 IP。

提示:尝试添加多个标题有时也可以绕过速率限制。

> **有验证码时绕过速率限制**

你一定遇到过谷歌验证码，而测试网站。这些都是一些方法的帮助下，你可以绕过它。

1.  尝试从请求正文中删除 CAPTCHA 参数
2.  尝试添加一些与参数长度相同的字符串
3.  继续拦截，向入侵者发送请求。有时，它可能会产生意想不到的结果。

![](img/a6fb4bc01af3ea6b19f91ccb639ce80d.png)

> **绕过某些字符的速率限制**

1.  在电子邮件末尾添加空字节(%00)有时可以绕过速率限制。
2.  尝试在电子邮件后添加一个空格字符。(未编码)
3.  有助于绕过速率限制的一些常用字符:%0d，%2e，%09，%20

我知道还有很多方法可以绕过速率限制，请随意分享。

我希望你喜欢它。

请登录我的博客网站查看更多精彩的文章和博客:[https://Blog . theinfosecguy . m](https://blog.theinfosecguy.me/post/bypassing-rate-limit-like-pro/)e

在 LinkedIn 上找到我:[http://linkedin.com/in/keshav-malik](http://linkedin.com/in/keshav-malik-22478014a/)/