# 👩‍💻IW 周刊#38:缓存中毒，XSS 有效载荷，Akamai 和亚马逊 S3 桶，智能合同中的混合模糊，SSO，区块链安全审计，等等…

> 原文：<https://infosecwriteups.com/iw-weekly-38-cache-poisoning-xss-payloads-akamai-and-amazon-s3-buckets-hybrid-fuzzing-in-860ce4225eee?source=collection_archive---------3----------------------->

![](img/61e047251df8f1a83797eb9b269a8c09.png)

作者图片

**学习如何使用**[**【XSS】有效载荷，获得高达$44，625**](https://youtu.be/-856s1vnWHU) **的奖金。**

嘿👋

欢迎来到#IWWeekly38，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。

IWCON2022 终于在昨晚光荣落幕了，❤️，谢谢你的加入。我希望你玩得开心，学到了新东西😊请[在这里分享您的反馈](https://forms.gle/TnTTbj9MUhq5eoqs7)以帮助我们为您制作更好的下一个版本:)

回到今天的 NL，这里是我们本周的首选:7 篇文章，6 个线程，5 个视频，2 个 Github repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。

激动吗？让我们跳进来吧👇

# 📝7 篇 Infosec 文章(5+ 2 初学者友好型)

**# 1**[**@ TarunkantG**](https://twitter.com/TarunkantG)**分享了一个发生在** [**Akamai 和亚马逊 S3 桶**](https://spyclub.tech/2022/12/14/unusual-cache-poisoning-akamai-s3/) **之间的缓存中毒的独特案例。**

**# 2**[**@ pmnh _**](https://twitter.com/pmnh_)**[**@ usman mansha**](https://twitter.com/UsmanMansha420)**为了** [**绕过 Akamai WAF 实现远程代码执行【P1】**](https://h1pmnh.github.io/post/writeup_spring_el_waf_bypass/)**在一个 Spring Boot 应用上使用了 Spring 表达式语言注入。请继续阅读，了解他们是如何做到的。****

****#3 阅读如何**[**@ jzeerx**](https://mobile.twitter.com/jzeerx)**得到 SSTI 从而导致** [**任意文件阅读上一个亚洲领先的支付系统**](https://medium.com/@jazdprince/doing-it-the-researchers-way-how-i-managed-to-get-ssti-server-side-template-injection-which-66b239ca0104) **。****

****# 4**[**GAF nit Amiga**](https://twitter.com/gafnitav)**揭露了** [**AWS ECR Public 中的一个重大安全漏洞，外部参与者可以在其中删除、更新和创建图像、图层和标签**](https://blog.lightspin.io/aws-ecr-public-vulnerability) **。****

****#5 阅读** [**奥马尔·哈什姆的**](https://twitter.com/OmarHashem666) **详细文章在那里他解释了**[**CVE-2022–42710 旅程中直线浮现的 E3 系列追寻从 XXE 到储藏库的路径——XSS**](https://omar0x01.medium.com/cve-2022-42710-a-journey-through-xxe-to-stored-xss-851d74dfe917)**。****

## **初学者友好的-**

****# 1**[**Pratik Gaikwad**](https://www.linkedin.com/in/pratikgaikwad3/)**发现了一个** [**权限提升漏洞**](https://medium.com/@h4ck3rp4tik/privilege-escalation-leads-to-deleting-other-users-account-and-company-workspace-access-control-7b709eb88ef) **导致属于其他人和组织的账号和工作区被删除。****

****# 2**[**Abdelrhman Allam**](https://twitter.com/sl4x0)**创建了关于** [**单点登录【SSO】**](https://sl4x0.medium.com/all-about-single-sign-on-sso-c5c3d8e90b6f)**的综合博文。****

# **🧵6 趋势线程(4 + 2 初学者友好型)**

****#1 你想学智能合约** [**混合模糊化**](https://twitter.com/PatrickAlphaC/status/1601232979627761664?s=20&t=AN8sXUaWjiqprq83pXxhcg) **？读读帕特里克·柯林斯**[](https://twitter.com/PatrickAlphaC)****的这篇惊人的帖子。******

******#2 想要改进您的智能合同审计吗？阅读** [**帕绍夫的**](https://twitter.com/pashovkrum) **线程关于他如何计划成为一名** [**高级智能合同审计师**](https://twitter.com/pashovkrum/status/1602279751271563265?s=20&t=AN8sXUaWjiqprq83pXxhcg) **。******

******[**Csanuragjain**](https://twitter.com/csanuragjain/)**始乱终弃的名单** [**值得注意的区块链安全审计机构**](https://twitter.com/csanuragjain/status/1601825712033308673?s=20&t=AN8sXUaWjiqprq83pXxhcg) **随同公布的审计结果。********

******# 4**[**MixBytes**](https://twitter.com/MixBytes/)**分享了他关于** [**如何分析一份智能合同审计报告**](https://twitter.com/MixBytes/status/1603812222416715776?s=20&t=AN8sXUaWjiqprq83pXxhcg) **的技巧。******

## ****初学者友好的-****

******# 1**[**Het Mehta**](https://twitter.com/hetmehtaa/)**提供了一份** [**免费安全运营中心(SOC)认证**](https://twitter.com/hetmehtaa/status/1604486233320992769?s=20&t=AN8sXUaWjiqprq83pXxhcg) **的清单。******

******#2** [**阿比谢克米娜**](https://twitter.com/Aacle_) **分享了他对于** [**对付 Waf 的**](https://twitter.com/Aacle_/status/1603549855745449984) **的惊人技巧。******

# ****📽️ 5 个有见地的视频(3 + 2 初学者友好)****

******#1******

******#2 看这个** [**RCE 通过热罐子对换苹果的故事**](https://www.youtube.com/watch?v=A-O-irpqUWQ) **作者**[**@ Frans Rosen**](https://twitter.com/fransrosen)**。******

******#3 学习如何** [**fuzz 以太坊智能合约**](https://www.youtube.com/watch?v=vQTexNuWDrM) **使用** [**针鼹**](https://github.com/crytic/echidna) **通过**[**@ trailofbits**](https://twitter.com/trailofbits)**。******

## ****初学者友好的-****

****想知道 XSS 最常用的有效载荷是什么吗？看看这个由[**@ gregxsunday**](https://twitter.com/gregxsunday)**制作的视频，其中他** [**讨论了为什么、如何以及在哪里使用 XSS 的有效载荷，从而获得高达 44，625 美元**](https://youtu.be/-856s1vnWHU) **的奖金。******

******#2 在 NahamCon2022EU 上**[**@ tomnom nom**](https://twitter.com/TomNomNom)****命令行数据-扯皮** **上看这个惊艳的谈话。********

# ****新⚒️协议 GitHub 库和工具****

******# 1**[**Coinspect Security**](https://twitter.com/coinspect)**已经创建了一个** [**铸造测试**](https://github.com/coinspect/learn-evm-attacks) **集合，其中包括攻击、bug 赏金声明以及 EVM 链上的潜在漏洞。******

******#2** [**Immunefi 的**](https://twitter.com/immunefi) [**协作库**](https://github.com/immunefi-team/Web3-Security-Library%5C) **旨在提供您开始或扩展您对在线安全的理解所需的所有材料。******

# ****💰1 个工作警报****

****1 号 PhonePe 正在招聘一名安全工程师。结账详情 [**此处**](https://www.linkedin.com/jobs/view/3372153715) **。******

# ****💸和我们一起做广告💸****

****我们希望与来自世界各地的出色的 infosec、pen testing 和道德黑客团队、品牌和公司合作。****

******如果你想在我们 27k+的网络安全爱好者社区做广告，** [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) **与我们合作。******

****这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。****

# ****在我们说再见之前…****

****如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨****

****如果您有问题、意见或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。****

****下周再见。****

****大量的爱
编辑团队，
Infosec 报道****

*****这份简讯是我们与“神奇大使”合作制作的。*****

*****资源贡献者:* [*尼基尔·阿梅曼、*](https://twitter.com/NikhilMemane09) [*巴维什·哈马尔卡尔*](https://twitter.com/bhavesharmalkar) *、* [*莫希特·赫姆钱德尼*](https://twitter.com/mohitkchandani) *、* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) *、* [*马尼凯什·辛格*](https://twitter.com/X71n0?t=WYKqmnE22AY_ZAq73FeCOA&s=09)****

*****通迅格式由:* [*哈迪克辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，* [*维奈库马尔*](https://twitter.com/R007_BR34K3R) *，* [*西达尔特*](https://twitter.com/illucist_) *和*阿尤什辛格* *。******