# 我是如何接管公交订票网站的经理账户的？

> 原文：<https://infosecwriteups.com/how-i-take-over-the-managers-account-in-bus-booking-website-168c56430302?source=collection_archive---------0----------------------->

嘿，各位黑客和臭虫猎人们，

我是 Ramalingasamy M . K(安全研究员)。

2 月 1 日，我预订了一辆公共汽车去我朋友在 redbus.com 的家。过了一段时间，我想了想有多少网站可以预订巴士。我在谷歌上搜索了一些关于汽车预订的信息，找到了网站。我不应该透露网站名称。就叫它 target.com 吧

![](img/4b53b110a00795d4ae86344cc796846e.png)

我们开始吧

在那个网站上，我看到一个登录，它要求手机号码，突然我想，肯定可以使用响应操作登录！！。但是我的错它不起作用。

![](img/d48690ca8790a7920618fc83e3a4623d.png)

之后我看到一个 OTP 发到我手机上，是 4 位数的 OTP。我试试 OTP 蛮力。因为我认为没有速率限制算法，所以我可以使用 Turbo 入侵者来暴力破解 OTP。

> 但是，我应该如何增加影响呢？..

![](img/7479f718d7a4d2ec3d900f6662e516f8.png)

思考！！！

一个小时后，我明白了为什么我们要寻找在 target.com 工作的员工的联系电话。在谷歌搜索了一分钟后，我得到了一位高级经理的联系电话。

我试着联系那个号码，嘣，它透露了经理联系号码的电子邮件 id 和用户 id。

![](img/66c431663ca65f0ec800ae148c30aafb.png)

感谢您阅读这篇文章。

请关注我，获取更多关于寻找 bug 的文章

在 Instagram 上关注我:[https://www.instagram.com/ram_0x_infosec/](https://www.instagram.com/ram_0x_infosec/)

在推特上关注我:【https://twitter.com/Ram00733925 

在 Linkedin 上联系我:【https://www.linkedin.com/in/ram0xinfosec/ 

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。[查看更多详情并在此注册。](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)