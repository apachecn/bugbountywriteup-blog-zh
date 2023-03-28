# 子域枚举 TryHackme 编写

> 原文：<https://infosecwriteups.com/subdomain-enumeration-tryhackme-writeup-55e9fdbe47e7?source=collection_archive---------0----------------------->

## 寻找子域的艺术

欢迎回来，伟大的黑客们，我又一次提出了精彩的内容，这些内容是基于寻找有效的子域名，对**网络应用安全**或**漏洞追踪**有用。

![](img/ad56e6be9f079ab2cb7d5022590c5844.png)

这是在该地区大范围内搜寻虫子的侦察方法之一。让我们进入寻找给定目标的有效子域的艺术。

查找子域时涉及的不同枚举方法有:

1.  **暴力**
2.  **新的**
3.  **虚拟主机**

让我们来解决 **Tryhackme** 平台给出的问题。

> **什么是以 B 开头的子域枚举法？**

*暴力*

> **什么是以 O 开头的子域枚举法？**

*OSINT*

> **什么是以 V 开头的子域枚举法？**

*虚拟主机*

我们来讨论 OSINT—**TLS**/**SSL**方法。 **SSL** 代表**安全套接字层**和 **TLS** 代表**传输层安全**，使用该证书的目的是因为 CA( **证书颁发机构**为该域创建证书)阻止恶意行为。我们为新的子域名使用这项服务。

> 示例:crt.sh

这提供了一个显示当前和历史数据的可搜索证书。

> **2020–12–26 在 crt.sh 上登录了什么域？**

*store.tryhackme.com*

接下来，我们将了解搜索引擎。

**搜索引擎**

我们可以利用像 google.com 这样的搜索引擎找到子域。

制作一些高级过滤器来查找子域。

*   **site:www . example . com site:* . example . com**，通过使用这个过滤器我们可以得到某个特定域的所有子域。
*   这是我们实时用于子域的方法。

> **使用上述谷歌搜索发现的以 B 开头的 TryHackMe 子域是什么？**

*blog.tryhackme.com*

**DNS 暴力:**

Bruteforce DNS 是用于查找常用子域的枚举方法之一。

> **DNS recon 工具找到的第一个子域是什么？**

*api.acmeitsupport.thm*

**OSINT -Sublis3r:**

Sublis3r 是寻找子域的自动化工具。

> **sublist 3r 发现的第一个子域是什么？**

*web 55 . acmeitsupport . thm*

一些 DNS 不是公开托管的，而是作为私有 DNS 保存。在这种情况下，我们可以使用 FUZZ。

> **ffuf-w/usr/share/word lists/sec lists/Discovery/DNS/name list . txt-H " Host:fuzz . acmeitsupport . thm "-u**[**http://MACHINE _ IP**](http://MACHINE_IP)**-fs { size }**

这是使用 **FUZZ 查找子域的命令。**

> **发现的第一个子域是什么？**

三角洲

> **发现的第二个子域是什么？**

黄色