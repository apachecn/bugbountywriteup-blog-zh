# Shodan.io — TryHackme

> 原文：<https://infosecwriteups.com/shodan-io-tryhackme-42e2ae5b420?source=collection_archive---------0----------------------->

## 设备枚举

你好，了不起的黑客们，我想到了另一个基于枚举的很酷的博客。因此，不浪费任何时间，让我们进入博客。

Shodan 用于列举互联网上公开可用的设备。然后，通过使用 shodan 监视器，使用它来查找一系列 IP 地址中的漏洞。

我们可以使用 Shodan Dorking，这对内容发现很有用。

> 我们如何在 Shodan 上找到永恒之蓝？

*vuln:ms17–010*

> **Google 的 ASN 中 MYSQL 服务器的顶级操作系统是什么？**

*5 . 6 . 40–84.0-日志*

> **在 Google 的 ASN 中，MYSQL 服务器第二受欢迎的国家是哪个？**

*荷兰*

> **Google 的 ASN 下，nginx 的超文本传输协议和带 SSL 的超文本传输协议哪个更流行？**

*超文本传输协议*

> **在 Google 的 ASN 下，最受欢迎的城市是哪个？**

*山景*

> **谷歌在洛杉矶的 ASN 下，据 Shodan 说最顶级的操作系统是什么？**

*泛操作系统*

> **使用探索页面的顶部网络摄像头搜索，Google 的 ASN 有网络摄像头吗？是/否**

反对

通过***shod an monitor***用于监控您自己设备的任何漏洞或开放端口，并始终跟踪您自己的信息是否被泄露。但是这个功能只提供给高级用户。

值得注意的例子包括:

最佳开放端口(最常见)

最危险的漏洞(我们需要马上处理的东西)

感兴趣的港口(开放的不寻常港口)

漏洞的可能性

值得注意的知识产权(我们应该更深入调查的事情)。

有趣的是，你可以用这个来监控其他人的网络。你可以为 bug 奖金保存一个 IP 列表，如果 Shodan 发现任何问题，它会给你发电子邮件。

> **通过什么网址可以访问 Shodan Monitor？**

[*https://monitor.shodan.io/dashboard*](https://monitor.shodan.io/dashboard)

Shodan Dorks 习惯于从网站上寻找有用的信息。

> **哪个呆子让我们找到被勒索病毒感染的电脑？**

*has _ 截图:真加密关注*

shodan for google chrome 的可用扩展:

[](https://chrome.google.com/webstore/detail/shodan/jjalcfnidlmpjhdfepjhjbhnhkbgleap) [## 肖丹

### Shodan 插件告诉你网站托管在哪里(国家，城市)，谁拥有 IP 和其他什么服务/端口…

chrome.google.com](https://chrome.google.com/webstore/detail/shodan/jjalcfnidlmpjhdfepjhjbhnhkbgleap) 

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。查看更多详情并在此注册。

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)