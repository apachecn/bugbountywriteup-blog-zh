# Bug 搜索的侦查方法！

> 原文：<https://infosecwriteups.com/recon-methodology-for-bug-hunting-e623120a7ca6?source=collection_archive---------0----------------------->

> 什么是侦察或信息收集？

它指的是收集尽可能多的关于目标系统的信息，以找到渗透到系统中的方法的过程。这是执行安全评估时的一个重要阶段和准备阶段。

一个强有力的信息收集阶段决定了一个好的和一个坏的渗透测试者。

一个优秀的渗透测试人员 90%的时间都花在扩大攻击面上，因为他知道这就是全部。其余的 10%只是用合适的工具发出正确的命令，成功率很高。

![](img/462ce90f0e7e60deff968d1851ddf429.png)

> 子域枚举:-

**子域枚举是侦察阶段最重要的部分。**

它可以帮助您扩大范围，这可以揭示安全评估范围内的许多子域，这将为您提供更多的目标来找到漏洞，并可能增加您获得更多好错误的机会。

*对于子域枚举，你可以选择很多不同的策略和工具*

你可以和:-

*   [子查找器](https://github.com/projectdiscovery/subfinder)
*   [资产发现器](https://github.com/tomnomnom/assetfinder)
*   [Dnsenum](https://github.com/fwaeytens/dnsenum)
*   谷歌多金
*   [Dnsdumpster](https://dnsdumpster.com/)
*   [病毒总数](https://www.virustotal.com/gui/home/search)

> 让我们来分析一下网络技术

*   [Wapplyzer 扩展](https://addons.mozilla.org/en-US/firefox/addon/wappalyzer/) — Wappalyzer 是一个浏览器扩展，揭示了网站上使用的技术。它可以检测内容管理系统、电子商务平台、网络服务器、框架、分析工具等等。
*   Netcraft -它也可以让你发现目标使用的后端技术。
*   用建造——这将让你知道你的目标在他们的后端使用的是哪种技术。
*   [什么网站](https://github.com/urbanadventurer/WhatWeb) -这是下一代网络扫描仪，可以识别网站使用的技术。这个工具预装在您的 Linux 中。
*   [Whois](https://who.is/) —搜索 Whois 数据库，查找域名和知识产权所有者信息，并查看许多其他统计数据。

> 网络扫描是指获取附加信息并根据足迹阶段收集的信息执行更详细侦察的过程。

*   发现活动主机、IP 地址和活动主机的开放端口。
*   发现操作系统和系统架构
*   发现主机上运行的服务
*   发现主机上运行的漏洞

> 使用 Nmap(网络映射器)

Nmap 是由戈登·里昂创建的免费开源网络扫描仪。Nmap 用于通过发送数据包和分析响应来发现计算机网络上的主机和服务。Nmap 为探测计算机网络提供了许多功能，包括主机发现和服务以及操作系统检测

**让我们继续进一步的信息收集流程:-**

> Google Dorking :-

Google Dorking 是我们使用高级搜索操作符来完善搜索并专注于搜索主题的过程。

[](https://en.wikipedia.org/wiki/Google_hacking) [## 谷歌黑客-维基百科

### 谷歌黑客，也称为谷歌多金，是一种黑客技术，使用谷歌搜索和其他谷歌应用程序…

en.wikipedia.org](https://en.wikipedia.org/wiki/Google_hacking) 

> Github Dorking :-

GitHub Dorking 使用特定的搜索关键字在公共存储库中查找敏感信息。这和 Google Dorking 差不多。你可以手动执行，这比自动化要好的多，在这里投入你的大量时间来获取一些有用的信息。如果你想选择自动化，那就选择 [Gitdorker](https://github.com/obheda12/GitDorker) 。

[](https://obheda12.medium.com/gitdorker-a-new-tool-for-manual-github-dorking-and-easy-bug-bounty-wins-92a0a0a6b8d5) [## git dorker——一个更好的工具来执行 GitHub 呆子和抓取容易的 Bug 赏金获胜

### 如何以及为什么手动 GitHub dorking 比自动工具集更容易获得 bug 赏金。

obheda12.medium.com](https://obheda12.medium.com/gitdorker-a-new-tool-for-manual-github-dorking-and-easy-bug-bounty-wins-92a0a0a6b8d5) [](https://github.com/techgaun/github-dorks) [## techgaun/github-dorks

### 文件名:。npmrc _auth npm 注册表身份验证数据文件名:。dockercfg auth docker 注册表验证数据…

github.com](https://github.com/techgaun/github-dorks) 

> 肖丹

Shodan 是一个搜索引擎，它让用户使用各种过滤器找到连接到互联网的特定类型的计算机

[](https://medium.com/@sw33tlie/finding-a-p1-in-one-minute-with-shodan-io-rce-735e08123f52) [## 与 Shodan.io (RCE)一起在一分钟内找到 P1

### 还有什么比用 Shodan.io 在两分钟内找到 P2 更好的呢？简单的回答:更高的严重性，更少的…

medium.com](https://medium.com/@sw33tlie/finding-a-p1-in-one-minute-with-shodan-io-rce-735e08123f52) 

> 返程机

这是一个信息收集网站，可以让你看到任何网站的历史，他们如何改变，他们执行什么更新。

[](https://archive.org/web/) [## 互联网档案:回溯机

### 查看互联网档案馆图书借阅的最新动态

archive.org](https://archive.org/web/) 

> OSINT 框架

开源情报(Open-source intelligence)指的是对公开信息的收集和分析，这些信息大多来自在线来源。

 [## OSINT 框架

### (T) -表示必须在本地安装和运行的工具的链接(D) - Google Dork，了解更多信息:Google…

osintframework.com](https://osintframework.com/) 

> 内容发现

内容发现是找到每一个隐藏文件、端点、每一个参数的过程，这些都可以在后续的测试中使用。

**有多种工具可用于此，一些最流行的工具有:-**

*   [捉鬼敢死队](https://github.com/OJ/gobuster)
*   Dirb
*   恐怖分子

> 一些外卖:-

*   任何类型的信息收集都不要依赖单一的工具或方法，至少使用两种工具或方法并验证结果。
*   更注重手动而不是自动化。
*   花更多时间收集信息

希望这对你们有用

黑客快乐！

推特手柄:-【https://twitter.com/Xch_eater 