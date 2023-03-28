# 【负责任的披露】我是如何通过其他用户账户预订电影票的

> 原文：<https://infosecwriteups.com/responsible-disclosure-how-i-could-have-booked-movie-tickets-through-other-user-accounts-2db26a037b4c?source=collection_archive---------8----------------------->

***注意:该漏洞已上报，现已修复。***

[**AGS 电影院**](https://www.agscinemas.com/) 是泰米尔纳德邦钦奈著名的剧院之一。他们去年推出了自己的电影票预订网站和应用程序。

![](img/99d09036b25b07a1b48603fec29837d3.png)

“灰色墙壁上的两台闭路电视摄像机”，斯科特·韦伯[在](https://unsplash.com/@scottwebb?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这篇文章是关于我在 *AGS 电影院*发现的一个简单的漏洞，我可以利用这个漏洞在没有任何用户互动的情况下轻松侵入其他用户的账户。

这使我可以通过设置新密码来完全访问其他用户的帐户。我可以查看机票历史、他们的信用钱包和其他私人信息。

**Suresh Kumar，***macapp studio*(AGS 影院的*技术合作伙伴)的首席执行官迅速承认了这个问题，并修复了它。有不少像他一样谦逊的人会接受这种安全漏洞，因为很多人会在未经他们允许的情况下测试他们的网站时与我对抗。*

# 黑客是如何工作的

每当用户在 AGS 电影院忘记密码时，他们可以通过在忘记密码弹出窗口中输入他们的电话号码来重置密码。

然后，AGS 电影院会向该电话号码发送一个 4 位数的代码，用户必须输入该代码才能设置新密码。

我试图强行破解 www.agscinemas.com 上的 4 位数密码(*例如 3286* )，在 5-6 次无效尝试后没有被阻止。有趣的是，忘记密码端点中没有速率限制。

我尝试接管我自己的帐户，并成功地为我的帐户设置了新密码。然后我可以用这个密码登录我自己被黑的账户。

# 黑客的概念验证视频

正如你在视频中看到的，我可以通过暴力破解发送到用户电话号码的密码来为用户设置新密码。

`POST /php/otpverify.php HTTP/1.1`

`Host: www.agscinemas.com`

`mobile=XXXXXXXXXX&randomnums=XXXX`

暴力破解“randomnums”成功地让我为任何 AGS 电影院的账户设置了新密码。

# 披露时间表

2018 年 2 月 21 日:发现 Bug。

2018 年 2 月 22 日:报告已发送至 MacAppStudio 团队。

2018 年 2 月 23 日:得到首席执行官的认可。

2018 年 2 月 24 日:他们解决了问题。

## 感谢您通读🙌🏼。如果您觉得这篇文章有用，请使用👏按钮，并通过我们的圈子分享。