# 我是如何开始子域名收购链并黑掉 100 家公司的

> 原文：<https://infosecwriteups.com/how-i-started-a-chain-of-subdomain-takeovers-and-hacked-100s-of-companies-770d8f84885e?source=collection_archive---------0----------------------->

这一切开始于六个月前，当时我发现了 [Frans Rosén](https://medium.com/u/1979b6518264?source=post_page-----770d8f84885e--------------------------------) 的博客([https://labs . detectify . com/2014/10/21/敌对子域接管使用-herokugithubdesk-more/](https://labs.detectify.com/2014/10/21/hostile-subdomain-takeover-using-herokugithubdesk-more/) )关于这个叫做子域接管(DNS 劫持)的有趣漏洞。在那之后，我在不同的公司发现了这些漏洞，在披露我发现这些问题的公司之前，让我们详细讨论一下这个漏洞。

对于这些公司来说，跟踪这种攻击并不容易，全球大约有 16-17 家服务提供商容易受到这种安全问题的攻击，其中一些服务提供商包括 AWS、Heroku、Statuspage、Github、Pantheon、Bitbucket、Desk、Squarespace、Unbounce、Fastly 和 Shopify。

## 那么如何发现这个漏洞呢？

查找带有以下错误消息的子域:

![](img/96f14cf4efc9308b2e478d1cda731c82.png)

图片提供: [Ed](https://medium.com/u/ac1a79a0eeea?source=post_page-----770d8f84885e--------------------------------)

更多信息可以访问 [Ed](https://medium.com/u/ac1a79a0eeea?source=post_page-----770d8f84885e--------------------------------) 的 Github:[https://github.com/EdOverflow/can-i-take-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz)

## 攻击场景

1.  您的公司开始使用新的服务，例如新的博客门户
2.  你的公司将一个子域指向博客门户，例如[blog.your-domain.com](http://support.your-domain.com/)
3.  您的公司停止使用此服务，但不会删除指向博客门户的子域重定向。
4.  攻击者注册该服务并声称该域是他们的。服务提供商不做任何验证，DNS 设置已经正确设置。
5.  攻击者现在可以构建真实网站的完整克隆，添加登录表单，重定向用户，窃取凭据(如管理员帐户)，cookies 和/或完全破坏您公司的商业信誉。

## 那么，如何利用这个漏洞呢？

1.  你基本上开始蛮力迫使子域找到其中一个错误页面。
2.  当你找到一个，你检查子域的 CNAME，并注册为你的服务提供商，这就是现在你已经接管了子域。

利用这种攻击是非常容易的，并且很难跟踪它的域名所有者。

我最初对这些 bug 如此着迷，以至于我只是在寻找 bug 时才寻找它们，我在大约 100 家公司发现了这些问题，如耐克、兰博基尼、CNN、Musixmatch、网飞、Alienvault、索尼、奥迪、123contactform、Nearbuy 和许多我不允许透露的私人项目。下面附上几张截图:

![](img/77b2d49df2ad98e62eaa3c47ec2cebb6.png)

兰博基尼

![](img/eb161983449afbc18a3944ad6488be8c.png)

沃尼克

![](img/171df965c2ea7ed94a1d270acf0b31ba.png)

Exotel

![](img/4f1829c7a5f12ec99f06e61b08b1f3cc.png)

Delhivery

![](img/178a7ce2f911f6a222612c634ba09fe3.png)

近购

![](img/238f412d0a678cbcf12617558b620493.png)

紫色

![](img/f9e43472aaa7d21dae2b9756fef297d7.png)

索尼移动

![](img/e904bd881b629f9d6d73171423444717.png)

Flood.io

![](img/a478be981bf5c0f9e2dcf3d0e1503725.png)

Appknox

![](img/47190ca26bf046de953910704b038c0a.png)

美国有线新闻网；卷积神经网络

![](img/c3f1a68dbd3f7d6dc9a548cf628b21fe.png)

索尼利夫

![](img/6b532dddf92340c469934ad7c791dd29.png)

奈基

![](img/6a339bdb49404d912f41684ac23bcd7e.png)

123 联系方式

![](img/07a807a33482b47e6d144218d13c91c0.png)

Wync 音乐

![](img/0355a855d7aa70a5c36f01f16a467708.png)

音乐比赛

![](img/35d7d436df6ab3ed75bcf829b781975f.png)

向上保护

![](img/7ee2711c96a61f80b3346a179b2c568c.png)

艾伦沃特

![](img/5521d79319c12f10e484b4868d1ee649.png)

网飞

![](img/9ad0418bc87a2136f34b3116ead61aac.png)

奥迪

这份名单还在不断增加，而且不会很快停止。

我只是希望这篇博客对你有所帮助，让你更好地理解子域收购。