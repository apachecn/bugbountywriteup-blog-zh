# 一个关于 IDOR To Account Takeover 的小故事

> 原文：<https://infosecwriteups.com/a-short-story-of-idor-to-account-takeover-b36f3983ecba?source=collection_archive---------2----------------------->

![](img/86c177af732b01a0dc1c424d286d61be.png)

大家好。我是 Jeya Seelan，一名安全研究员和一名 Bug 猎人。这是我的第一篇关于昆虫赏金的文章。我们将看到一个关于伊多尔的小故事，以及我是如何通过它接管你的账户的。

在进入细节之前，让我们看看什么是 IDOR。

## 什么是 IDOR？

IDOR 代表不安全的直接对象引用，它是一种访问控制漏洞。根据 OWASP，IDOR 发生..

> *当应用程序基于用户提供的输入提供对对象的直接访问时，就会出现不安全的直接对象引用*。由于此漏洞，攻击者可以绕过授权，直接访问系统中的资源，例如数据库记录或文件。不安全的直接对象引用允许攻击者通过修改用于直接指向对象的参数值来绕过授权并直接访问资源。这样的资源可以是属于其他用户的数据库条目、系统中的文件等等。”**

*![](img/4b795e6acce8ab09205c87d64d4af3a1.png)*

*让我们看一个例子，考虑两个用户用户 A 和用户 B。用户 A 有一个 ID 为 1000 的文档，用户 B 有一个 ID 为 1002 的文档。*

*在正常情况下，用户 A 只能访问 ID 为 1000 的文档，用户 B 只能访问 ID 为 1002 的文档。但是在这里，如果用户 A 将文档 ID 更改为用户 B 的 ID -1002，用户 A 就能够访问用户 B 的文档。这是由于破坏了访问控制和不安全的直接对象引用。*

## *我是如何接管受害者账户的*

*让我们把这个网站想象成 REDACTED.COM。我已经开始在网站上搜索，并开始测试诸如 XSS 和其他错误，但我没有成功，因为输入的内容已经过消毒，处理得当。有时我检查了配置文件更新页面，发现了一个 API 端点 **POST /updateprofiledata***

*请求体包含一个 JSON 请求，其 ID 参数的值为用户 ID 号。所以我决定使用 Repeater 来处理这个请求，我将值改为受害者 ID Boom，响应为 200 OK，受害者帐户上的详细信息得到了更新。但是仍然没有严重的影响，因为我们只能更改姓名和地址等详细信息。*

*![](img/3419f41761e01a3f4df215ba605dced9.png)*

*过了一段时间，我想增加 IDOR 的影响力。所以我决定寻找其他的终点，但是我没有运气。所以我使用同一个端点，并在 JSON 请求 Boom 中添加了一个包含攻击者电子邮件的电子邮件参数。我的请求成功了，电子邮件被更改为攻击者电子邮件。现在，我转到密码重置页面来重置用户密码。最后，由于 IDOR 的存在，我已经接管了受害者帐户。*

*感谢您的阅读。如果你喜欢读我的文章，请鼓掌并关注社交媒体手柄:-)。*

*领英:[https://www.linkedin.com/in/jeyaseelans](https://www.linkedin.com/in/jeyaseelans)*

*推特:[T3【https://twitter.com/jeyaseelans86】T5](https://twitter.com/jeyaseelans86)*

*中:[https://medium.com/@jeyaseelans](https://medium.com/@jeyaseelans)*