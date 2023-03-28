# 伊多尔:初学者指南

> 原文：<https://infosecwriteups.com/idor-a-beginners-guide-bd5f9bf893fe?source=collection_archive---------1----------------------->

嗨，很高兴带着一个关于网络开发的新话题回来，IDOR。IDOR 是一种访问控制漏洞。让我们马上进入正题。

> IDOR 代表不安全的直接对象引用

如前所述，这是一种访问控制漏洞。访问控制漏洞是指攻击者可以访问并非针对他们的信息或操作。当 web 服务器接收到用户提供的输入以检索对象时，可能会出现 IDOR 漏洞。这里的对象是指文件、数据、文档等。由于对输入数据过于信任，web 应用程序不会验证用户是否可以访问所请求的对象。

![](img/0f3d2bdfa4c28b30c931fcd998c9358e.png)

# **如何发现并利用 IDOR 漏洞？**

如前所述，IDOR 漏洞依赖于更改用户提供的数据。这种用户提供的数据主要可以在以下三个地方找到:

*   查询组件
*   过帐变量
*   饼干

## **查询组件:**

在网站中发出请求时，查询组件数据在 URL 中传递。看看下面这个网址的截图:

![](img/27998febe49da66cc324e10255770ba7.png)

查询组件

URL 可以细分为:

**协议:** https://

**域名:** website.thm

**页面:**/个人资料

**查询组件:** id=23

在这里，我们可以看到/profile 页面被请求，值为 **23** 的参数 **id** 被传入查询组件。该页面可能会向我们显示个人用户信息。如果我们将 id 参数更改为其他值，那么我们可以查看其他用户的数据。

## **发布变量:**

有时，检查网站上表单的内容可以发现易受 IDOR 攻击的字段。例如，以更新用户密码的表单的以下 HTML 代码为例。

您可以在 **<输入>** 标签中看到，用户的 id 正在一个隐藏字段中被传递给 web 服务器。将 value 参数更改为另一个 user_id 可能会导致更改另一个用户帐户的密码。

## **饼干:**

Cookies 用于记住您的会话，以保持登录网站。通常，它发送的会话是一长串难以猜测的随机文本，例如 **8dghlsjh9k4dfkmzakfi3hr0k3n，**，web 服务器安全地使用它来检索您的用户信息并验证您的会话。有时，经验不足的开发人员可能会将用户信息存储在 cookie 本身中，例如用户的 id。因此，更改此 cookie 的值可能会导致显示另一个用户的信息。你可以参考下面的例子，看看它们可能是什么样子。

`GET /user-information HTTP/1.1
Host: website.thm
Cookie: user_id=9
User-Agent: Mozilla/5.0 (Ubuntu;Linux) Firefox/94.0`

`Hello Jon!`

`GET /user-information HTTP/1.1
Host: website.thm
Cookie: user_id=5
User-Agent: Mozilla/5.0 (Ubuntu;Linux) Firefox/94.0`

`Hello Martin!`

黑客快乐:)

> 参考:
> THM 赛博 3 号房问世

[![](img/01dbc2827853e082bc25bdd8996e1998.png)](https://www.buymeacoffee.com/ssudarshan)

“给我买杯咖啡”是一个全球平台，数百万人在这里为创作者和艺术家提供经济支持。

Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和充满力量的讨论会议。 [**查看更多详情，在此注册。**](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)