# 7000 美元奖金，Web3 漏洞搜索，API 黑客，IDOR，用表情符号触发 XSS，XSS 传单，以及更多…

> 原文：<https://infosecwriteups.com/7000-bounty-web3-bug-hunting-api-hacking-idor-triggering-xss-with-emojis-xss-flyer-and-much-fb4c51fb26ef?source=collection_archive---------4----------------------->

![](img/8fcd054b39fe0bdad1f9d91bb8f1e46a.png)

嘿👋

欢迎来到#IWWeekly21，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。

不敢相信已经是第 21 版了😍

我们的关系现在已经 21 周了。我们很好奇，你喜欢我们的每周信息安全汇编吗？在 Twitter 上让我们知道并标记我们 [@InfoSecComm](https://twitter.com/InfoSecComm) 。这将提高团队的士气，并帮助我们更加努力地工作，在每个星期一为您带来最好的信息安全😊

目前，这里是我们本周的首选:5 篇文章，4 个线程，3 个视频，2 个 Github repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。

激动吗？让我们开始吧👇

# 📝5 篇信息安全文章

**#1 如果你开始了解使用表情符号触发 XSS 会怎样？阅读**[**@ Patrik Fabian**](https://medium.com/@fpatrik)**[**如何通过使用表情符号**](https://medium.com/@fpatrik/how-i-found-an-xss-vulnerability-via-using-emojis-7ad72de49209) **发现一个 XSS 漏洞。****

****[**中的#2 基于浏览器的 desync 攻击**](https://portswigger.net/research/browser-powered-desync-attacks?s=09#connection-locked) **文章**[**@ James Kettle**](https://twitter.com/albinowax)**向您展示如何将受害者的 web 浏览器变成 Desync 交付平台，通过暴露单服务器网站来转移请求走私前沿。******

****对于 id 不可预测的 IDORs 的 bug 报告，总是存在激烈的争论。阅读关于[**@ rez 0**](https://twitter.com/rez0__)**的文章对于** [**为什么它们是有效漏洞，应该修复**](https://rez0.blog/hacking/cybersecurity/2022/08/18/unpredictable-idors.html) **。******

******#4 阅读如何** [***@KevTheHermit 的***](https://twitter.com/KevTheHermit) **团队** [**在控制网页面板中发现重大漏洞包括【RCE】**](https://www.immersivelabs.com/blog/we-discovered-major-vulnerabilities-in-control-web-panel-heres-how-we-found-them/)**、账号劫持和注入漏洞。******

******# 5**[**@ Andri**](https://deb0con.medium.com/)**分享他关于杰克森数据绑定库中不受欢迎的 RCE bug 的个人笔记。阅读** [**他如何从 Grab (RCE 特有的虫子)**](https://deb0con.medium.com/how-i-earned-a-7000-bug-bounty-from-grab-rce-unique-bugs-5e5037c5a58d) **中获得 7000 美元的虫子赏金。******

# ****🧵4 趋势线程****

******# 1**[**@ Nithin R**](https://twitter.com/thebinarybot/)**关于** [**选择合适的 bug 赏金程序**](https://twitter.com/thebinarybot/status/1559171143390695424?t=Y7eEGh_Div5vhIIXE0_Z7w&s=19) **的详细线程。如果你是初学者或面临问题，它会有很大的帮助。******

******#2 打算跳进 Web3 捕虫？这条 twitter 线程可以** [**引导你通过**](https://twitter.com/shabarkin/status/1559165267418161153?s=21&t=np9gs8GJJcC84YBOQM8u6g)[**@ Pavel Shabarkin**](https://twitter.com/shabarkin/)**在 Web3 平台** **上做好狩猎准备。******

******#3 下面是整个** [**30 天 AWS 漏洞**](https://twitter.com/devansh3008/status/1561291780053536768?s=20&t=ru5FTFhcYY-YCgfRxOh3Pw)**thread by**[**@ Devansh Bordia**](https://twitter.com/devansh3008/)**。******

******# 4**[**@ sec r0**](https://twitter.com/sec_r0/)**分享他们的** [**XSS 传单连同开箱 XSS 旁路过滤器。**](https://twitter.com/sec_r0/status/1559922398119358464?t=ZHY7rzSUvW4zYqUSSnJeow&s=19)****

# ****📽️ 3 有见地的视频****

******# 1**[**David Bomball**](https://twitter.com/davidbombal?t=K3gxK05uTbJ4HN3HZ50hjg&s=09)**访谈科里球又名**[**hAPI _ hacker**](https://twitter.com/hAPI_hacker)**并探讨关于** [**API 黑客的一切以及他的免费 API 黑客课程**](https://youtu.be/CkVvB5woQRM) **。******

******#2** [**通过**](https://youtu.be/sUYLTSnbCDA) [**科里球**](https://twitter.com/hAPI_hacker) **学习如何黑 API****。******

******# 3**[**@ Gunnar Andrews**](https://twitter.com/G0LDEN_infosec)**分享他的** [**亲身经历以及在 DEFCON**](https://youtu.be/HPoQvPiT8dU) **上与顶级黑客对话所学到的东西。******

# ****⚒️2 Github 库和工具****

******# 1**[**nullt3r**](https://twitter.com/nullt3r)**的工具名为** [**Jf scan 是一个包装器，围绕着一个超高速端口扫描器**](https://github.com/nullt3r/jfscan) **Masscan 和 Nmap。它旨在简化在各种格式的目标上扫描开放端口的工作。******

****GraphQuail 是一个 Burp 套件扩展，它提供了一个测试 GraphQL 端点的工具包。 **通过**[**@ force sunsee**](https://twitter.com/forcesunseen)**在 Github 上阅读该工具目前实现的特性。******

# ****💰1⚠️工作预警****

******#1** [**安真科技私人有限公司招聘实习生**](https://www.linkedin.com/posts/pooja-chandarana-2a63b2131_cybersecurity-cybersecurityinterns-iso27001-activity-6966053648225243136-QuZR?utm_source=linkedin_share&utm_medium=android_app) **。******

******职位数量:12 个******

******津贴:是******

# ****💸和我们一起做广告💸****

****我们希望与来自世界各地的惊人的信息安全、笔式测试和道德黑客团队、品牌和公司合作。如果这听起来像你， [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) 与我们合作。****

******——————******

****这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。****

# ****在我们说再见之前…****

****如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨****

****如果您有问题、评论或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。****

****下周再见。****

****很多爱****

****编辑团队，****

****[信息安全报道](https://infosecwriteups.com/)****

*****这份简讯是我们与“神奇大使”合作制作的。*****

*****资源贡献者:* [*比马尔·k·萨胡*](https://twitter.com/srb1mal) *，* [*尼基尔·梅马尼*](https://twitter.com/NikhilMemane09) *，* [*莫希特·凯姆昌达尼*](https://twitter.com/mohitkchandani) *，*[](https://twitter.com/illucist_)**，* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) *，******

*******通迅格式由:*[*Vinay Kumar*](https://twitter.com/R007_BR34K3R)*[*Hardik Singh*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09)*和* [*西达尔特*](https://twitter.com/illucist_) *。********

*******如果您希望加入我们的大使频道并为时事通讯投稿，请在 Twitter 上用您的 discord 用户名向我们发送 DM。*******