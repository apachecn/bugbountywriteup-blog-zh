# 👩‍💻IW 周刊# 39:10，000 美元奖金，零点击账户接管，存储 XSS，开放重定向漏洞，SQL 注入，RCE，侦察技术，以及更多…

> 原文：<https://infosecwriteups.com/iw-weekly-39-10-000-bounty-zero-click-account-takeover-stored-xss-open-redirection-2e6bf480bc26?source=collection_archive---------1----------------------->

![](img/aa12a1c895d92a330eae6dc14090acc2.png)

**[**举报脸书卷轴**](https://bugreader.com/social/write-ups-general-delete-any-video-or-reel-on-facebook-11-250--100965?fbclid=IwAR16bED_J9-xqmnVq98jSp-JIyrCAhtfnns7gsdMGpFpEVZKr6VL7tVPebA) 有缺陷的裁剪和修剪功能，奖励 10，000 美元😍**

**嘿👋**

**欢迎来到#IWWeekly39，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。**

**IWCON2022 终于光荣落幕了，❤️，谢谢你的加入。我希望你玩得开心，学到了新东西😊请[在这里分享您的反馈](https://forms.gle/TnTTbj9MUhq5eoqs7)以帮助我们为您制作更好的下一个版本:)**

**回到今天的 NL，这里是我们本周的首选:7 篇文章，6 个线程，5 个视频，2 个 GitHub repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。**

**激动吗？让我们跳进来吧👇**

# **📝7 篇 Infosec 文章(5+ 2 初学者友好型)**

****# 1**[**Bassem m . Bazzoun**](https://twitter.com/bassemmbazzoun)**因报道** [**脸书电影胶片**](https://bugreader.com/social/write-ups-general-delete-any-video-or-reel-on-facebook-11-250--100965?fbclid=IwAR16bED_J9-xqmnVq98jSp-JIyrCAhtfnns7gsdMGpFpEVZKr6VL7tVPebA) **中的错误裁剪和修剪功能** [](https://bugreader.com/social/write-ups-general-delete-any-video-or-reel-on-facebook-11-250--100965?fbclid=IwAR16bED_J9-xqmnVq98jSp-JIyrCAhtfnns7gsdMGpFpEVZKr6VL7tVPebA)**而获得 1 万美元的奖励。****

****# 2**[**Eugene Lim**](https://twitter.com/spaceraccoonsec)**查看了** [**Zoom 的代码，发现了一个有趣的攻击向量，用于存储 XSS**](https://spaceraccoon.dev/analyzing-clipboardevent-listeners-stored-xss/) **。****

****#3 阅读** [**@Yaala 的**](https://twitter.com/yaalaab) **脸书手机 app** **中** [**零点击账号接管和 2FA 旁路的发现。**](https://medium.com/@yaala/account-takeover-and-two-factor-authentication-bypass-de56ed41d7f9)**

****#4 安全研究员**[**Alessandro gro PPO**](https://twitter.com/kiks7_7)**写了一个非常** [](https://blog.hacktivesecurity.com/index.php/2022/12/21/cve-2022-2602-dirtycred-file-exploitation-applied-on-an-io_uring-uaf/)**有趣的 bug**[**(CVE-2022–2602 无公开漏洞利用)以一个释放后使用漏洞影响 io_uring 子系统**](https://blog.hacktivesecurity.com/index.php/2022/12/21/cve-2022-2602-dirtycred-file-exploitation-applied-on-an-io_uring-uaf/) **。****

****#5 阅读如何**[**@ canmustdie**](https://twitter.com/canmustdie?lang=en)**在苹果** **的一个子域上发现** [**一个开放重定向漏洞。**](/bypass-apples-redirection-process-with-the-dot-character-c47d40537202)**

## **初学者友好的-**

****#1 如果你想通过入侵 graphql APIs 赚取丰厚的奖金，请阅读这篇由** [**阿努格拉的**](https://twitter.com/cyph3r_asr) **关于** [**graphql 测试**](https://anugrahsr.in/graphql-pentesting-for-dummies_part1/) **的惊人博客。****

****# 2**[**Dheeraj**](https://twitter.com/Dheerajydv19)**曾** [**写过一篇关于初学社会工程指南**](https://dheerajydv19.hacklido.com/d/151-social-engineering-a-beginners-guide-part-2) **的详细帖子。****

# **🧵6 趋势线程(4 + 2 初学者友好型)**

****#1** [**伊斯梅尔**](https://twitter.com/0x0SojalSec) **贴了一个很棒的线程讲解** [**不安全的 CORS 配置漏洞**](https://twitter.com/0x0SojalSec/status/1604872799956668417?t=BnLKaxMukT9XhNRHbYW6WA&s=19) **。****

****2。**[**Abhishek Meena**](https://twitter.com/Aacle_)**编制了一份** [**bug 赏金自动化 oneliner 命令**](https://twitter.com/Aacle_/status/1605945331585261568?s=20&t=lvZX04hzQ94GLl3fPq8X_Q]) **。****

****3。**[**Tomo**](https://twitter.com/tom_eth_dev/)**共享链接到包含智能合约漏洞报告** [**集合的概念文档**](https://twitter.com/tom_eth_dev/status/1606832631282565122) **。****

****4。**[**Maik Ro**](https://twitter.com/maikroservice/)**分享了一份** [**日常生活中黑客心理健康小技巧**](https://twitter.com/maikroservice/status/1603284608061263873) **。****

## **初学者友好的-**

****#1 看这个惊人的线程由**[**Abhishek Meena**](https://twitter.com/Aacle_/)**关于** [**HTTP 基本认证头**](https://twitter.com/Aacle_/status/1606332133345071114?s=20&t=lvZX04hzQ94GLl3fPq8X_Q) **。****

****#2 看** [**Eno Leriand 的**](https://twitter.com/0x3n0/) **线程关于 Simmeth 系统中导致远程代码执行的** [**SQL 注入漏洞**](https://twitter.com/0x3n0/status/1606959414766075904)**(CVE-2022–44015)。****

# **📽️ 5 个有见地的视频(3 + 2 初学者友好)**

****#1** [**阅读 RFC 可能会很无聊，导致较少的 bug 猎人会关注它**](https://youtu.be/4ZsTKvfP1g0) **，但是**[**@ securinti**](https://twitter.com/securinti)**向你展示了阅读 RFC 的力量，在一个案例中，他甚至获得了**[**【3100 美元的赏金**](https://hackerone.com/reports/1086108) **。****

****# 2**[**@ _ John Hammond**](https://twitter.com/_JohnHammond)**带你了解来自 CTF 纳哈姆科纽的 MMORPG 挑战赛，** [**在那里你会了解到一个会导致漏洞**](https://youtu.be/Kl2dNllRY-4) **的实现缺陷。****

****#3** [**学习 CodeQL，GitHub**](https://youtu.be/VrF1RwnJzBk) **开发的代码分析引擎，自动化安全检查，配合**[**@ live overflow**](https://twitter.com/LiveOverflow)**调查 GraphQL 解析器。****

## **初学者友好的-**

****# 1**[**@ AseemShrey**](https://twitter.com/AseemShrey)**访谈** [**@ArmanSameer95 又名 Tess**](https://twitter.com/ArmanSameer95) **在他** [**深入细节并揭示他的侦察技巧**](https://www.youtube.com/watch?v=1-IB8TE0Hro) **。****

****#2 Yuvraj，Priceline 的 bug bounty 计划排名第一的黑客，** [**在接受**](https://www.youtube.com/watch?v=qIHk_hAMqhw)[**@ AseemShrey**](https://twitter.com/AseemShrey)**采访时深入探讨 SSRFs** **。****

# **新⚒️协议 GitHub 库和工具**

****# 1**[**Coinspect Security**](https://twitter.com/coinspect)**已经创建了一个** [**铸造测试**](https://github.com/coinspect/learn-evm-attacks) **集合，其中包括攻击、bug 赏金声明以及 EVM 链上的潜在漏洞。****

****#2** [**Immunefi 的**](https://twitter.com/immunefi) [**协作库**](https://github.com/immunefi-team/Web3-Security-Library%5C) **旨在提供您开始或扩展您对在线安全的理解所需的所有材料。****

# **💰1 个工作警报**

**排名第一的 Payatu 正在招聘具有 1-5 年工作经验的人员担任各种安全职务。 [**详情请看这里的**](https://twitter.com/payatulabs/status/1604776072218021889?t=w9A-AT_yMJ1tiaT8wT9n2w&s=19) **。****

# **💸和我们一起做广告💸**

****我们希望与来自世界各地的出色的 infosec、pen testing 和道德黑客团队、品牌和公司合作。****

****如果你想在我们 27k+的网络安全爱好者社区做广告，** [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) **与我们合作。****

**这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。**

## ****在我们说再见之前……****

**如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨**

**如果您有问题、意见或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。**

**下周再见。**

**大量的爱
编辑团队，
Infosec 报道**

***这份简讯是我们与“神奇大使”合作制作的。***

***资源贡献者:* [*尼基尔·阿梅曼、*](https://twitter.com/NikhilMemane09) [*巴维什·哈马尔卡尔*](https://twitter.com/bhavesharmalkar) *、* [*莫希特·赫姆钱德尼*](https://twitter.com/mohitkchandani) *、* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) *、* [*马尼凯什·辛格*](https://twitter.com/X71n0?t=WYKqmnE22AY_ZAq73FeCOA&s=09)**

***通迅格式由:* [*哈迪克·辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) *，* [*西达尔特*](https://twitter.com/illucist_) *，* [*阿尤什·辛格*](https://twitter.com/AyushSingh1098) *。***

## **[点击此处](https://weekly.infosecwriteups.com/)订阅时事通讯，每周一直接发送到您的收件箱:)**