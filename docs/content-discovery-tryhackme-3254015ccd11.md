# 内容发现试用版

> 原文：<https://infosecwriteups.com/content-discovery-tryhackme-3254015ccd11?source=collection_archive---------0----------------------->

嗨，了不起的黑客伙伴们，我制作了一个有趣的主题网络内容发现。这在虫子奖励中很有用，也是侦查中最重要的东西。

内容可以是不同的类型，如图像、文件、视频等。

有三种主要方法来发现网页上的内容，它们是:

手动、自动、Osint(开源智能)方法。

![](img/e2d96c6dddb320e7e25fba1993558db5.png)

> **M 开头的内容发现方式是什么？**

*Ans:手动*

> **以 A 开头的内容发现方法是什么？**

*答:自动化*

> **以 O 开头的内容发现方法是什么？**

*Ans: OSINT*

让我们研究一下手动发现，手动发现的查找方式有很多，其中一个就是 *robots.txt，robots.txt 文档规定了不允许哪个页面显示或抓取网页。*

> **robots . txt 中不允许网络爬虫查看的目录是什么？**

*Ans: /staff-portal*

接下来，我们继续通过 sitemap.xml 进行手动发现，它提供了页面所有者喜欢在网站上列出的内容。

> **sitemap . XML 文件中可以找到的秘密区域的路径是什么？**

*Ans: /s3cr3t-area*

下一步是通过 HTTP 头进行手动发现。HTTP 头有时会透露重要信息，如 web 服务器版本和使用的技术，以及使用的编程语言。这可能有助于发现 web 应用程序中的漏洞。

对于使用命令*[***http://machine-IP***](http://machine-ip)***-v .****

> ***X 标志头的标志值是什么？***

**Ans: THM{HEADER_FLAG}**

*下一个是通过框架栈的手动发现。对于详细版本和更重要的细节，我们可以看到文档部分*

> ***框架管理门户的标志是什么？***

**答:THM { CHANGE _ DEFAULT _ CREDENTIALS }**

*按照文档上的说明，您将获得该标志。*

*下一条新闻——谷歌黑客/呆子*

*谷歌呆子习惯于从谷歌搜索引擎获取定制内容。从你可以从网站上得到暴露的密码或隐藏的东西。*

> *可以按 ***站点、inurl、filetype、intitle*** 过滤*
> 
> ***什么 Google dork 操作符可以用来只显示特定网站的结果？***

**答:地点:**

*Wappalyzer 是一个在线工具，扩展用于查找网络技术和版本号。*

> *什么在线工具可以用来识别一个网站运行的是什么技术？*

**Ans: Wappalyzer**

*Waybackmachine 是一个历史档案，用于查找任何以前的 web 内容，无论这些内容现在是否存在。*

> ***way back 机器的网址是什么？***

**安:*[*https://archive.org/web/*](https://archive.org/web/)*

*Github 是另一个重要的网站，我们可以在其中查找敏感文件，如配置文件、密码、授权文件等等。Git 是一个版本控制系统，可以跟踪项目中的文件。*

> ***什么是 Git？***

**Ans:版本控制系统**

*Osint 的内容发现— S3 存储桶、S3 存储桶是由 AWS 提供的存储服务。在这种情况下，有些文件是公共分配的，有些是私有的。以防设置不正确而导致漏洞。*

> *s3 桶的格式是 http(s):// **{name}。**[**s3.amazonaws.com**](http://s3.amazonaws.com/)*

*一种类似的自动化方法是使用公司名称，后面跟着常用术语**{ name }**-资产、 **{name}** -www、 **{name}** -public、 **{name}** -private 等等。*

> ***亚马逊 S3 桶以什么 URL 格式结尾？***

**Ans: .s3.amazonaws.com**

*最后，我们将看到自动化发现，与手动发现相比，它简单、容易且耗时。为此，我们可以使用 ***ffuf、dirb*** 和 ***gobuster*** 等等。*

> *用于 **ffuf** 的 Ex*

```
****user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://MACHINE_IP/FUZZ****
```

> *Ex for **dirb***

```
*user@machine**$** dirb http://MACHINE_IP/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt*
```

> *ex for***gobuster****

```
*user@machine**$** gobuster dir --url http://MACHINE_IP/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt*
```

> ***以“/mo…”开头的目录名称是什么被发现了？***

**答:/月**

> ***被发现的日志文件的名称是什么？***

**Ans: /development.log**

# *🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。查看更多详情并在此注册。*

*[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)*