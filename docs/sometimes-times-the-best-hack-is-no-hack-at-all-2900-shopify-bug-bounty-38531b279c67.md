# 有时候，最好的黑客根本不是黑客——2900 美元的 Shopify Bug 赏金

> 原文：<https://infosecwriteups.com/sometimes-times-the-best-hack-is-no-hack-at-all-2900-shopify-bug-bounty-38531b279c67?source=collection_archive---------1----------------------->

## 访问控制是关键。

![](img/f496be51ab768d719e0da501f1ae401b.png)

照片由[陈信宏·K·苏雷什](https://unsplash.com/@ashin_k_suresh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

[访问控制漏洞](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)被[开放 Web 应用安全项目(OWASP)](https://owasp.org/Top10/) 列为 2021 年排名第一的 web app 安全风险。当应用程序变得越来越复杂，具有更多 API 端点和特性时，开发和遵守策略以维护适当的授权控制会变得更加困难。

许多系统(不仅仅是软件)的一个重要安全组件是**最小特权**的概念。用户仅被授予读取、写入和/或访问文件、API 等的权限。，他们需要执行他们的角色的功能。这可能是在内部开发/公司团队的环境中，但这在 web 应用程序功能的环境中也是有意义的。

**特权提升**是一种破坏访问控制的类型，用户可以在没有适当协议或授权的情况下获得更高级别/角色的特权。通常，当用户获得管理员权限时，或者当未登录的用户可以执行用户操作时，会出现这种情况。这种缺乏控制的严重程度因攻击者一旦获得信息后可以访问或修改的信息类型而异。

# **漏洞利用(如果我们可以这样称呼的话……)**

HackerOne 用户`0x50d`最近[在 Shopify 中发现了一个特权提升破坏访问控制漏洞](https://hackerone.com/reports/1417288)，获得了 2900 美元的奖金。通常，在特权提升问题中，攻击者需要欺骗服务器、API 等。，他们实际上并没有特权，但这种利用非常简单！

`0x50d`发现`[https://plus-website.shopifycloud.com/admin.php?](https://plus-website.shopifycloud.com/admin.php?_page=1)_page=1`是一个管理面板(即只有管理员才能访问)，无需验证即可访问。在本例中，我们的特权提升错误是从未登录到 admin。这很可能是通过 Google Dorking 发现的，这是一种“不需要编程知识”的搜索某些文档、密码、日志文件等的方法。有可能被泄露。

在一个非常透明的评分理由中，Shopify 讨论了为什么这个简单的 bug 被给予了 4.6(中等)的严重性分数:

> 此身份验证问题提供了对受影响站点上`/admin`的访问。注意`/admin/*`路径会像预期的那样被重定向到 auth。因此，尝试在管理面板中进行更改将会失败，并阻止完整性影响。但是，仍然存在对根页面上有限的合作伙伴档案信息的无意读取访问，这导致了较低的机密性影响。(来源:[hacker one 上的 Shopify Sec 团队)](https://hackerone.com/reports/1417288)

因此，用户能够获得读取访问权限，但不能获得任何其他形式的访问权限。这就是纵深防御原则非常重要的原因。这个管理面板中可能有另一个漏洞，这可能会使这个错误成为一个更大的问题。恭喜`0x50d`找到你应得的 2900 美元的虫子奖金！

```
**Want to Connect?**Please consider contacting me at roberto.cyberkid@gmail.com following me on Medium, [buying me a coffee](https://www.buymeacoffee.com/robertocyberkid), following me on [twitter](https://twitter.com/CyberKidLama), or connecting with me on [LinkedIn](https://www.linkedin.com/in/roberto-lama-9a126a123/)!
```

*来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。* [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*