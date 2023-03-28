# Netcat、SMTP 和 self XSS 有什么共同点？存储的 XSS

> 原文：<https://infosecwriteups.com/what-do-netcat-smtp-and-self-xss-have-in-common-stored-xss-a05648b72002?source=collection_archive---------1----------------------->

如果你正在读这封信，你可能想知道这是什么？这是在开玩笑吗？答案是否定的，这不是一个诱饵，这是一个把小问题串联起来，从自我的 XSS 转向完全成熟的 XSS 的故事。

![](img/6be99913dc9d5aa4642915edaf63383a.png)

只是一张随意的照片

当我开始黑一个私人程序时，这段旅程就开始了，在做研究时，我遇到了一个他们知道的自我 XSS 问题，这显然超出了范围。所以我继续挖掘，并开始了解该应用程序是如何工作的，它有 CRM 和其他相关的东西，这些东西现在并不重要。该应用程序有多个部分，其中一个是客户管理，具有常规功能，如创建/删除/修改客户，创建/附加发票…自我 xss 存在于客户视图的信息字段中。

> 邮箱 _ 此处

通过在电子邮件中添加一个简单的‘我能够打破 html，然后添加我自己的属性。

经过进一步挖掘，我发现如果你向发票电子邮件地址发送电子邮件，应用程序会检查发件人的电子邮件地址是否存在于客户的数据库中，如果找到，它会为该客户创建一个新的票证，但如果我们从非客户地址发送电子邮件呢？随着新票据一起创建了一个新的客户端。

现在，由于我可以控制部分客户信息(电子邮件地址)，我需要能够发送真正丢失格式的电子邮件，最简单的方法是使用 NETCAT。所以我打开了一个新的终端，查询他们的电子邮件服务器，开始构建漏洞。

首先，我必须建立一个工作的 XSS，然后我必须找出哪些字符破坏了 SMTP 语法，并对它们进行编码，最后得到一封这样的电子邮件

> onmouseover = ' alert & # 40 local storage . oauth & # 41 ' @ plenumsec . com

最后的攻击看起来像这样

```
nc -C mxa.mailgun.org 25 
>HELO plenumsec.com
>MAIL FROM: <'onmouseover='alert&#40localStorage.oauth&#41'@plenumsec.com> 
>RCPT TO: <random@target.com>
>DATA
From: Attacker <'onmouseover='alert&#40localStorage.oauth&#41'@plenumsec.com>
To: Victim  <random@target.com>
Subject: Urgent
Date: Wed, 26 Sep 2018 14:21:26 -0400
Hello,
This is a stored xss poc.
Goodbye.
```

现在，攻击者只需等待员工访问客户端管理页面或执行包括攻击者电子邮件在内的任何操作。该公司的电子邮件是完全可以猜测的，因此可以通过暴力和自动化的过程成为目标。

有人会说，这种攻击本来可以通过实施 SPF 来避免，但有时安全性对企业来说并不方便，所以他们不得不做出妥协。

感谢阅读，

问候，全体会议