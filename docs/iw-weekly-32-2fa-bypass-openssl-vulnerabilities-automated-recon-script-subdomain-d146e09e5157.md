# 👩‍💻IW 周刊#32: 2FA 旁路，OpenSSL 漏洞，自动侦察脚本，子域接管，DNS 劫持，等等…

> 原文：<https://infosecwriteups.com/iw-weekly-32-2fa-bypass-openssl-vulnerabilities-automated-recon-script-subdomain-d146e09e5157?source=collection_archive---------0----------------------->

![](img/2ed4f78ac99511e934b3359ec4a0f77c.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

**这些** [**多个漏洞导致远程代码执行(RCE)**](https://rohit-soni.medium.com/chaining-multiple-vulnerabilities-leads-to-remote-code-execution-rce-on-paytm-e77f2fd2295e) **在其中一家支付服务商**上。

嘿👋

欢迎来到#IWWeekly32，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。

在我们开始之前，我们很想知道您是否查看了全球最大的虚拟网络安全会议和网络活动 [**IWCON**](https://iwcon.live/) 的演讲者阵容😍🙌

日期是 2022 年 12 月 17 日至 18 日，这次会比上一次更大🔥

[**点击此处**](https://iwcon.live/) 查看活动详情，并在活动结束前预订座位！(你真的不想错过)

回到今天的 NL，这里是我们本周的首选:7 篇文章，6 个线程，5 个视频，2 个 Github repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。

激动吗？让我们跳进来吧👇

# 📝7 篇 Infosec 文章(5+ 2 初学者友好型)

**#1** [**阿卡什哈马尔**](https://twitter.com/AkashHamal0x01) **分享了关于** [**2FA 因信息泄露和访问控制不当而绕过的有趣见解。**](https://link.medium.com/DKDZwJzmzub)

**# 2**[**Wiz**](https://twitter.com/wiz_io)**发表博客详细介绍了我们应该注意的** [**OpenSSL 漏洞。**](https://wiz.io/blog/critical-openssl-vulnerability-everything-you-need-to-know) **做给它看。**

**#3 结账** [**ProjectDiscovery.io 的**](https://twitter.com/pdiscoveryio) **精彩博文来** [**构建一个快速的一次性自动化侦查脚本。**](https://blog.projectdiscovery.io/building-one-shot-recon/)

**#4 愿到** [**了解更多 JavaScript 原型污染攻击？**](https://www.appknox.com/security/prototype-pollution-attacks)**[**Appknox**](https://twitter.com/appknox/)**搞定你。****

****#5 了解如何**[**Rohit Soni**](https://twitter.com/streetofhacker)**[**利用多个漏洞在其中一个支付服务提供商上获得远程代码执行(RCE)。**](https://rohit-soni.medium.com/chaining-multiple-vulnerabilities-leads-to-remote-code-execution-rce-on-paytm-e77f2fd2295e)****

## ****初学者友好的-****

******#1 阅读** [**尼南**](https://twitter.com/_nynan) **综合分析** [**子域接管(SDTO)、DNS 劫持、悬空 DNS、CNAME 误配置**](https://medium.com/@nynan/what-i-learnt-from-reading-217-subdomain-takeover-bug-reports-c0b94eda4366) **。******

******#2 发现什么**[**Aditya Singh**](https://twitter.com/imrook1337)**关于** [**使用工具让你的侦查过程变得更简单。**](https://www.cyberick.com/post/these-4-tools-will-make-your-recon-process-easier-and-effective-bug-bounty)****

# ****🧵6 趋势线程(4 + 2 初学者友好型)****

******#1** [**艾米莉·索奇**](https://twitter.com/emiliensocchi) **在他的一个网络应用评估中解释了** [**他是如何让 AzureAd 租户接管的。**](https://twitter.com/emiliensocchi/status/1587917156842278913?s=46&t=BHdPyu4Rkq-pwCL6jZKtBw)****

******# 2**[**Md Ismail Sojal**](https://twitter.com/0x0SojalSec)**共享一个**[**XSS 有效载荷列表**](https://twitter.com/0x0SojalSec/status/1588257753478275072?s=20&t=DObQX5Z3lQakB9pOnmBhiQ) **。黑客攻击时，请将它们放在手边。******

******#3** [**如果你想黑掉智能合约但又不确定从何下手，**](https://twitter.com/maikroservice/status/1586273298685759488?t=wh78HO8Sh0olhkMQHQ6XPg&s=19) **查看此推文由** [**Maik Ro 的**](https://twitter.com/maikroservice/) **。******

******#4 探索本列表中的** [**bug 赏金提示绕过 2FA**](https://twitter.com/Aacle_/status/1586522129649967104?t=CjQpRMuw573bTGLbZn6dOw&s=19) **通过**[**Abhishek**](https://twitter.com/Aacle_)**。******

## ****初学者友好的-****

******#1** [**Maik Ro 的**](https://twitter.com/maikroservice) **线程** [**包含 web3 黑客资源的精选列表，用于深入了解 web3 安全性。**](https://twitter.com/maikroservice/status/1586488340454449154?t=ivvBMnOME65aLX42A8-75Q&s=19)****

******#2 你想知道** [**如何成功获得 bug 赏金吗？我们应该如何设定我们的思想和工作目标？**](https://twitter.com/maikroservice/status/1587221407947231244?t=V_aBWoxfqi4BJ9qUlw-mZw&s=19) **别担心随着** [**Maik Ro 的**](https://twitter.com/maikroservice) **给你盖了。******

# ****📽️ 5 个有见地的视频(3 + 2 初学者友好)****

******#1 精神上的不法之徒谈论** [**Heartbleed，一个使用最广泛的加密库之一 OpenSSL 中的漏洞。**](https://youtu.be/ffx5IL1l4CA)****

******#2** [**了解一下服务器端模板注入**](https://youtu.be/FwTH0zDiLxM) **如同**[**@ _ John Hammond**](https://twitter.com/_JohnHammond)**解决了一个基于相同的实验室。******

******#3 GraphQL 往往有很多实现差异和 bug，看**[**@ AseemShrey**](https://twitter.com/AseemShrey)**的视频上** [**深度递归攻击和 GraphQL 自省**](https://youtu.be/nn5yUVwH0Zc) **。******

## ****初学者友好的-****

******#1 看如何**[**@ g0l den _ infosec**](https://twitter.com/G0LDEN_infosec)**自动化他的** [**子域枚举和侦察**](https://youtu.be/yQ1ilyQtno8) **。******

******#2** [**简历是获得你**](https://youtu.be/uqhOlOdwavU) **梦想工作的重要第一步，**[**@ thecybermentor**](https://twitter.com/thecybermentor)**分享一些写好简历的小技巧。******

# ****新⚒️协议 GitHub 库和工具****

******#1** [**ezXSS 是渗透测试员**](https://github.com/ssl/ezXSS) **和 bug 赏金猎人通过**[**ely esa**](https://github.com/ssl)**测试盲 XSS 的简便方法。******

******# 2**[**Leaky-paths 是一个特殊路径的集合**](https://github.com/ayoubfathi/leaky-paths) **链接到主要的 web CVEs、已知的错误配置、有趣的 API 等。由**[**@ _ ayoubfathi _**](https://twitter.com/_ayoubfathi_)**。******

# ****💰1 个工作警报****

******#1 信息安全顾问** **职位**[**A3S Tech&Co**](https://www.a3stech.co.in/)**多个职位空缺(0-2 年经验)。******

# ****💸和我们一起做广告💸****

******我们希望与来自世界各地的出色的信息安全、笔式测试和道德黑客团队、品牌和公司合作。如果这听起来像你，** [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) **与我们合作。******

****这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。****

## ******在我们说再见之前……******

****如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨****

****如果您有问题、评论或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。****

****下周再见。****

****很多爱****

****编辑团队，****

****[信息安全报道](https://infosecwriteups.com/)****

*****这份简讯是我们与“神奇大使”合作制作的。*****

*****资源贡献者:* [*阿尤什·辛格*](https://twitter.com/AyushSingh1098) *，* [*哈迪克·辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，* [*图欣·博斯*](https://twitter.com/tuhin1729_) *，* [*普拉莫德·库马尔*](https://twitter.com/NinjaFurrry?t=AYh1WBkceSJD-UvG2qETTg&s=09) *，* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) *，*****

******通迅格式由:* [*哈迪克辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09)*[*维奈库马尔*](https://twitter.com/R007_BR34K3R)*[*尼辛*](https://twitter.com/thebinarybot)*[*西达尔特*](https://twitter.com/illucist_) *和*********

******[点击这里加入我们的 22k 订阅者简讯](https://weekly.infosecwriteups.com/):)******