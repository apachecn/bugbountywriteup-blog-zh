# 黑客必备工具

> 原文：<https://infosecwriteups.com/must-have-tools-for-hacking-c2b75b332d2c?source=collection_archive---------1----------------------->

## 查找漏洞的有用工具

![](img/3aa377c358d2cdd54b6e73fcfc65c202.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的 [ThisisEngineering RAEng](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)

T4:我不在乎你做什么类型的工作。工具是其中很大的一部分。如果你是我工作的老追随者，你已经知道我是干什么的了。如果这是你第一次阅读我的作品，欢迎陌生人，我是一名黑客，今天我想与你分享一些我用来完成我的手艺的工具。

黑客是 *60%* 技能和 *40%* 工具。如果没有合适的工具，您可能会错过目标的宝贵信息。大多数新人都相信 ***BurpSuite*** ，但只有我们中的少数人知道，这个令人敬畏的软件并不具备一切，这就是为什么你需要一些额外的工具来找到那里最好的 bug 并为此获得报酬。

# 工具:

这些是我日常使用的一些工具。对漏洞搜寻有用的工具，排名不分先后。

## -Secapps 或 AppBandit

[](https://secapps.com) [## 信息安全工具和服务平台

### 不断增长的信息安全工具和服务的集合，以及不断扩大的专业…

secapps.com](https://secapps.com) 

AppBandit 是一个拦截攻击代理，因此通过其内置代理服务器的任何流量都可以被模拟。但是与其他代理不同，这不是一个要求，也不是它唯一做的事情。AppBandit 可以使用来自远程端点的数据，包括处理由 libpcap 和等效库捕获的 PCAP 数据。我们将使用 pown.js 中的“Pown Now”来捕获数据。为了方便起见，端点设置在本地，但是您可以将它放在任何地方，包括您的 Pi Zero W。

## -太棒了

[](https://github.com/guelfoweb/knock) [## GitHub - guelfoweb/knock: Knock 子域扫描

### 敲门子域扫描。在 GitHub 上创建一个帐户，为 guelfoweb/knock 开发做贡献。

github.com](https://github.com/guelfoweb/knock) 

Knockpy 是一个 python3 工具，旨在通过字典攻击快速枚举目标域上的子域。

## -子列表 3r

[](https://github.com/aboul3la/Sublist3r) [## GitHub - aboul3la/Sublist3r:渗透测试人员的快速子域枚举工具

### Sublist3r 是一个 python 工具，旨在使用 OSINT 枚举网站的子域。它帮助渗透测试人员和…

github.com](https://github.com/aboul3la/Sublist3r) 

Sublist3r 是一个 python 工具，旨在使用 OSINT 枚举网站的子域。它帮助渗透测试人员和 bug 猎人收集和聚集他们所针对的域的子域。Sublist3r 使用 Google、Yahoo、Bing、Baidu、Ask 等众多搜索引擎枚举子域名。Sublist3r 还使用 Netcraft、Virustotal、ThreatCrowd、DNSdumpster 和 ReverseDNS 枚举子域。

## **-IPV4info.com**

[http://ipv4info.com/](http://ipv4info.com/tools/)

**特点:**
— ipv4 分配表
—所有已分配和已分配区块的部分注册数据
—作为信息并公布自己的前缀
—IP v4 地址的地理位置数据
—IP 地址的所有域

# -什么 CMS

 [## 检测网站正在使用的内容管理系统-什么内容管理系统？

### 内容管理系统，简称 CMS，是一款旨在帮助用户创建和编辑网站的软件。这是…

whatcms.org](https://whatcms.org/) 

WhatCMS.org 处理来自世界各地的用户寻求了解他们正在使用的网站的请求。web 上有数百个内容管理系统在使用，它们的使用量相差很大。

## 目击者

[](https://github.com/FortyNorthSecurity/EyeWitness) [## GitHub-FortyNorthSecurity/witness:witness 是设计来抓取网站截图的…

### 目击者是专为采取网站截图，提供一些服务器头信息，并确定默认…

github.com](https://github.com/FortyNorthSecurity/EyeWitness) 

Witness 的设计目的是获取网站截图，提供一些服务器标题信息，并在可能的情况下识别默认凭证。

## -建造

[https://www.builtwith.com/](https://builtwith.com/about)

BuiltWith 成立于 2007 年，是一个网站分析器、线索生成、竞争分析和商业智能工具，为互联网提供技术采用、电子商务数据和使用分析。

## -贝特卡普

[](https://www.bettercap.org/) [## 贝特卡普

### ベッターキャップ!WiFi、蓝牙低能耗、无线 HID 劫持以及 IPv4 和 IPv6 网络的瑞士军刀…

www.bettercap.org](https://www.bettercap.org/) 

瑞士军刀用于 [WiFi](https://www.bettercap.org/modules/wifi/) 、[蓝牙低能耗](https://www.bettercap.org/modules/ble/)、无线 [HID 劫持](https://www.bettercap.org/modules/hid/)、 [IPv4 和 IPv6](https://www.bettercap.org/modules/ethernet) 网络侦察和 MITM 攻击。

## -瓦帕里斯

[](https://addons.mozilla.org/en-US/firefox/addon/wappalyzer/) [## 获取此扩展🦊火狐浏览器(美国)

### 为 Firefox 下载 Wappalyzer。识别网站上的技术

addons.mozilla.org](https://addons.mozilla.org/en-US/firefox/addon/wappalyzer/) 

Wappalyzer 是一个[浏览器扩展](https://outgoing.prod.mozaws.net/v1/f42a16047b2569583f4020259f7a78bb1512bdadf1e45ec423ad42fccc3272ea/https%3A//www.wappalyzer.com/download)，它揭示了网站上使用的技术。它检测[内容管理系统](https://outgoing.prod.mozaws.net/v1/a8441c495eb580145a31a8ed5e8f211d9a462d3ea990e661f0bb270814d10f19/https%3A//www.wappalyzer.com/technologies/cms)、[电子商务平台](https://outgoing.prod.mozaws.net/v1/2b3e4585e073040c75400684690374fbcd875d050d3ad741c2ea4940df1f24f8/https%3A//www.wappalyzer.com/technologies/ecommerce)、[网络服务器](https://outgoing.prod.mozaws.net/v1/ecb9f7f9dbb5dbc29340f49dddc480601cba8293b2ec7a81349943a6e0e6c73b/https%3A//www.wappalyzer.com/technologies/web-servers)、 [JavaScript 框架](https://outgoing.prod.mozaws.net/v1/51a354a185bc1b89a03a9c0d78b2804f25f7f615b1ea770bf36d9620bf661d51/https%3A//www.wappalyzer.com/technologies/javascript-frameworks)、[分析工具](https://outgoing.prod.mozaws.net/v1/654fecc5c6b06878083a7e4441e1181545c418c04ca9feacee84b27bfe33868c/https%3A//www.wappalyzer.com/technologies/analytics)和[更多](https://outgoing.prod.mozaws.net/v1/ed819a6892466b8189ff8ac14d44be190ffdc248d4db9745715ec8c6e2418bd8/https%3A//www.wappalyzer.com/technologies)。

## 摘要

不幸的是，或者幸运的是，这取决于你如何看待它，这些工具提供了如此多的功能，我无法在这里充分描述它。您可以使用它们进行子域发现、敏感文件发现、用户名枚举、抓取社交媒体网站等。虽然有些自动搜索漏洞的过程，这些不应该取代人工工作，敏锐的观察和直觉思维，祝你好运。