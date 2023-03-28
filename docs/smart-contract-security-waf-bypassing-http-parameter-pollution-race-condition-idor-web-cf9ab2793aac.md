# 👩‍💻智能合约安全、WAF 绕过、HTTP 参数污染、竞争条件、IDOR、Web 缓存中毒等等…

> 原文：<https://infosecwriteups.com/smart-contract-security-waf-bypassing-http-parameter-pollution-race-condition-idor-web-cf9ab2793aac?source=collection_archive---------1----------------------->

![](img/ce2b89537b20fb8042715f6b090b2946.png)

**了解** [**HTTP 参数污染**](https://twitter.com/sec_r0/status/1565746733878898688?t=yv5y8aJRuBWLtCE1ZjJDhQ&s=19)**——一个简单的测试就能获得丰厚的奖金。**

嘿👋

欢迎来到#IWWeekly23，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。

🤔在我们进入今天的版本之前，我们很想知道你是否喜欢这些版本。

您想要添加/删除某个特定部分吗？或者你希望我们有更多初学者友好的资源，或专家水平的见解…

在 Twitter 上让我们知道并标记我们 [@InfoSecComm](https://twitter.com/InfoSecComm) 。我们一定会在下一个版本中添加它，并给予您信任:)

今天，这里是我们本周的精选:5 篇文章，4 个线程，3 个视频，2 个 Github repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。

激动吗？让我们开始吧👇

# 📝5 篇信息安全文章

**#1 阅读如何**[**@ Lotus _ 619**](https://twitter.com/Lotus_619)**[**在 Hubspot**](https://med-mahmoudi26.medium.com/saving-more-than-100-000-website-from-a-watering-hole-attack-a22f63a37f94) **发现网页缓存中毒，安全保护 10 万多个网站。****

****#2** [**走向原子:**](https://ajpc500.github.io/talks/Blue-Team-Con-Going-Atomic/) **以技术为中心的紫色组队方式的优缺点由**[**@ ajpc 500**](https://twitter.com/ajpc500)**解释。****

****#3 如果你发现 WAF 绕过了 cool，那么你会喜欢阅读**[**@ s0md 3v**](https://twitter.com/s0md3v?lang=en)**[**关于绕过 ModSecurity for RCEs**](https://s0md3v.github.io/blog/modsecurity-rce-bypass)**的牛逼文章。******

******# 4**[**Monish Basaniwal**](https://link.medium.com/xvdV8KW32sb)**解释** [**通过在礼品卡网站**](https://monish-basaniwal.medium.com/the-million-dollar-hack-8163892bfe2f) **上查找种族条件和 IDOR，从而黑掉百万美元。******

******#5 阅读这篇令人惊叹的文章，了解一下** [**桑托什·库马尔·沙阿**](https://twitter.com/killmongar1996) **是如何发现** [**脱离乐队 rce 与 burpsuite 合作者**](https://notifybugme.medium.com/out-of-bond-remote-code-execution-rce-on-de-nederlandsche-bank-n-v-with-burp-suite-collaborator-2ce50260e2e4) **。******

# ****🧵4 趋势线程****

******#1 想了解活动目录安全性吗？**[**Rami**](https://twitter.com/drunkrhin0)**发了一个牛逼的帖子，里面包含了一个始乱终弃的** [**博客列表开始活动目录安全**](https://twitter.com/drunkrhin0/status/1564757368168099840?t=XD76G3cjmqMRNUb-3tWaoQ&s=19) **。******

******#2 对区块链&智能合约安全感兴趣？阅读这篇来自**[**@ Shashank**](https://twitter.com/cyberboyIndia/)**解释** [**智能合约**](https://twitter.com/cyberboyIndia/status/1564953200494489600?t=U3oddUElXCfYBFfimyKFiw&s=19) **中整数溢出和下溢的有价值的线程。******

******#3** [**这里有一个很棒的线索，可以帮助你找到收集开源情报的来源**](https://twitter.com/AseemShrey/status/1565333159180484608?t=Bwh-MEbbFi5g07Xq-tE8GA&s=19) **。******

******#4 了解** [**HTTP 参数污染**](https://twitter.com/sec_r0/status/1565746733878898688?t=yv5y8aJRuBWLtCE1ZjJDhQ&s=19) **—轻松获得丰厚奖金的测试。******

# ****📽️ 3 有见地的视频****

******#1 一个绝佳的** [**机会潜入智能合约黑客**](https://www.youtube.com/watch?v=DUdVA8rQKPg)**as**[**@ Nahamsec**](https://twitter.com/NahamSec)**与**[**@ HalbornSecurity**](https://twitter.com/HalbornSecurity)**合作开始一系列。******

******# 2**[**@ _ John Hammond**](https://twitter.com/_JohnHammond)**从 DEFCON**[**@ red team village _**](https://twitter.com/RedTeamVillage_)**CTF 和** [**用 PHP**](https://youtu.be/zK4VsJp3WGQ) **向我们展示了一些很酷的招数。******

******# 3**[**@ C3 Rb 3 ru 5d 3d 53 c**](https://twitter.com/c3rb3ru5d3d53c)**教我们** [**如何着手学习汇编语言**](https://www.youtube.com/watch?v=lFtxYgKooPI) **进行恶意软件分析和逆向工程。******

# ****⚒️2 Github 库和工具****

******# 1 hak scale by**[**@ hak Luke**](https://twitter.com/hakluke)**是一款基于 Golang 的工具，其中** [**允许用户跨多个系统**](https://github.com/hakluke/hakscale) **分发扫描/命令。******

******# 2**[**JWT-雷欧是一个 burpsuite 插件**](https://github.com/nccgroup/jwt-reauth) **，它缓存来自“auth”URL 的认证令牌，然后通过**[**@ NCCGroupInfosec**](https://twitter.com/nccgroupinfosec)**将它们添加为所有发往某个范围的请求的头。******

# ****💰1 个工作警报****

******#1 水电招聘初级安全工程师**[](https://jobs.hydro.com/job/Jaipur-Junior-Security-Engineer/845739801/)****。********

****[**此处适用**](https://jobs.hydro.com/job/Jaipur-Junior-Security-Engineer/845739801/) **。******

# ****💸和我们一起做广告💸****

******我们希望与来自世界各地的出色的 infosec、pen testing 和道德黑客团队、品牌和公司合作。如果这听起来像你，** [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) **与我们合作。******

******—————******

****这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。****

******在我们说再见之前……******

****如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨****

****如果您有问题、意见或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。****

****下周再见。****

****很多爱****

****编辑团队，****

****[信息安全报道](https://infosecwriteups.com/)****

*****这份简讯是我们与“神奇大使”合作制作的。*****

*****资源贡献者:* [*阿尤什·辛格*](https://twitter.com/AyushSingh1098) *，* [*比马尔·k·萨胡*](https://twitter.com/srb1mal) *，* [*西达尔特*](https://twitter.com/illucist_) *，* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) ， [*马尼凯什·辛格*](https://twitter.com/X71n0?t=WYKqmnE22AY_ZAq73FeCOA&s=09) *，*[](https://twitter.com/NikhilMemane09)****

******通迅格式由:* [*哈迪克·辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09)*[*西达尔特*](https://twitter.com/illucist_)*[*维奈·库马尔*](https://twitter.com/R007_BR34K3R)*[*阿尤什·辛格*](https://twitter.com/AyushSingh1098) *。*********

## ******来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！******