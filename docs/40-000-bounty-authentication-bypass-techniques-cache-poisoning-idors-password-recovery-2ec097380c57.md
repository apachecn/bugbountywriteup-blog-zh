# 👩‍💻40，000 美元奖金，认证旁路技术，缓存中毒，IDORs，密码恢复，以及更多…

> 原文：<https://infosecwriteups.com/40-000-bounty-authentication-bypass-techniques-cache-poisoning-idors-password-recovery-2ec097380c57?source=collection_archive---------0----------------------->

![](img/b31cf28d173a6e475c84501434e7b6ae.png)

照片由[米卡鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

[**【40，000 美元，用于发现微软基于 chromium 的新浏览器**](https://leucosite.com/Edge-Chromium-EoP-RCE/) **中的 3 个明显错误。**

嘿👋

欢迎来到#IWWeekly27，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。

在我们开始之前，我们很想知道您是否了解了全球最大的虚拟网络安全会议和网络活动 [**IWCON**](https://iwcon.live/) 的发言人阵容😍🙌

日期是 2022 年 12 月 17 日至 18 日，这次会比上一次更大🔥

[**点击此处**](https://iwcon.live/) 查看活动详情，并在活动结束前预订座位！(你真的不想错过)

回到今天的 NL，这里是我们本周的首选:7 篇文章，6 个线程，5 个视频，2 个 Github repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。

激动吗？让我们跳进来吧👇

# 📝7 篇 Infosec 文章(5+ 2 初学者友好型)

**# 1**[**@ Qab**](https://twitter.com/Qab?t=oHdc8T2x-ynG3KndEg6btg&s=09)**解释了他是如何通过在微软新推出的基于 chromium 的浏览器** **中发现 3 个明显的 bug 而赚了 4 万美元。**

**#2 阅读这篇关于**[**WhatsApp 中整数溢出导致远程代码执行的有趣文章在一个已建立的视频中调用**](/cve-2022-36934-an-integer-overflow-in-whatsapp-leading-to-remote-code-execution-in-an-established-e0fc4e2cd900)**by**[**@ secpy community**](https://twitter.com/SecPyCommunity?t=-e_NV9zoVH613FoxVr-ptw&s=09)**。**

**# 3**[**Ozgur Alp**](https://twitter.com/ozgur_bbh?t=2wvIvrTYx4sYDRbq35LFLw&s=09)**已经解释了他的一些惊人的** [**认证绕过技术**](https://www.synack.com/blog/exploits-explained-5-unusual-authentication-bypass-techniques/?utm_content=222702763&utm_medium=social&utm_source=twitter&hss_channel=tw-2485761421) **。**

**#4 Francesco Mariani 和他的朋友**[**Jacopo Tediosi**](https://twitter.com/jacopotediosi?t=cRHWjNDcLLg_e1MQrCChZw&s=09)**做了一个有趣的发现，关于一个** [**Akamai 错误配置，导致所有 Akamai 边缘节点**](https://medium.com/@jacopotediosi/worldwide-server-side-cache-poisoning-on-all-akamai-edge-nodes-50k-bounty-earned-f97d80f3922b) **上的全球服务器端缓存中毒。**

**#5 阅读此文关于** [**极光不当输入杀毒 bugfix 审查**](https://medium.com/immunefi/aurora-improper-input-sanitization-bugfix-review-a9376dac046f)**by**[**Immunefi**](https://twitter.com/immunefi)**。**

# 初学者友好的-

**#1 发现这个匿名的 18 岁少年** [**如何黑掉像优步**](https://securityboat.in/power-of-social-engineering-uber-hack-2022/) **这样的科技巨头。**

**#2 在这个故事中，Bergee's 解释了** [**他是如何由于缺乏服务器端邮件验证**](https://bergee.it/blog/blind-account-takeover/) **而接管一个账户的。**

# 🧵6 趋势线程(4 + 2 初学者友好型)

**#1 学习如何** [**科本利奥**](https://twitter.com/hacker_?lang=en) [**黑掉一家游戏公司**](https://twitter.com/hacker_/status/1575466233784258560?t=zJ_1V8cNTpzGWpUKHY3tBA&s=19) **的完整过程。**

**# 2**[**@ shrekysec**](https://twitter.com/shrekysec)**讲述他们如何能够** [**利用多个 IDORs 接管 admin 账户**](https://twitter.com/shrekysec/status/1575806883553837057?t=jMD_K7F4xqqjqtEYNW7WYA&s=19) **。**

**#3 阅读本线程至** [**了解智能合约中的事件**](https://twitter.com/cyberboyIndia/status/1574384225008357380?t=z_GxudOzQLw5kKCzCXh7Iw&s=19) **由**[**@ cyber boy India**](https://twitter.com/cyberboyIndia)**。**

**# 4**[**@ intidc**](https://twitter.com/intidc)**在一篇关于** [**的帖子中总结了他的研究，如何仅使用车牌**](https://twitter.com/intidc/status/1574263808607997953?s=46&t=dHrGNl2hw8yVyNsmQMxOJw) **就能追踪任何汽车的位置。**

# 初学者友好的-

**# 1**[**@ re conone**](https://twitter.com/ReconOne_bk)**分享如何** [**充分利用 subfinder**](https://twitter.com/ReconOne_bk/status/1574339345166704643?t=om66KkxyzlFUc7b4rswOPg&s=19) **这样强大的工具的小技巧。**

**#2 一份** [**学习伦理黑客**](https://twitter.com/7h3h4ckv157/status/1575875803744591872?t=IDk6pelugi64IU6sUoS3YQ&s=19) **的详尽资源清单由**[**@ 7h 3 H4 ckv 157**](https://twitter.com/7h3h4ckv157)**。**

# 📽️ 5 个有见地的视频(3 + 2 初学者友好)

**#1 观看此视频寻找** [**Intigriti 九月 XSS 挑战赛**](https://youtu.be/0H-p6WxX0WU) **的解决方案。**

**# 2**[**@ _ John Hammond**](https://twitter.com/_JohnHammond)**展示**[**@ PlexTrac**](https://twitter.com/PlexTrac)**，一个高效的 pentest 报告和管理平台。**

**# 3**[**@ the cybermentor**](https://twitter.com/thecybermentor)**分享一些炫酷的** [**OSINT 技巧使用密码恢复功能**](https://www.youtube.com/watch?v=yMuNNTSjQlc) **。**

# 初学者友好的-

**# 1**[**@ e11i0t _ 4 lders 0n**](https://twitter.com/e11i0t_4lders0n)**关于他** [**如何入门 bug 赏金和一些有见地的小技巧**](https://youtu.be/R94NfBeLeiI) **给初学 bug 猎人。**

**# 2**[**@ insider PhD**](https://twitter.com/InsiderPhD)**的 Bugcrowd LevelUpX 谈谈** [**如何(几乎)再也不上当**](https://youtu.be/Ae0s4ow7nWc) **。**

# 新⚒️协议 Github 库和工具

**# 1**[**云版有用的黑客招数**](https://github.com/carlospolop/hacktricks-cloud)**by**[**@ carlospolpm**](https://twitter.com/carlospolopm)**。**

**#2** [**Asnmap 是一个基于 Go 的 CLI 和库，用于通过**](https://github.com/projectdiscovery/asnmap)[**@ pdiscoveryio**](https://twitter.com/pdiscoveryio)**使用 ASN** **信息快速映射组织网络范围。**

# 💰1 个工作警报

在艾哈迈达巴德， **#1 eSecurity 拥有** [**2 个网络安全分析师职位和 2 个网络安全分析师实习生职位**](https://www.linkedin.com/posts/smitbshah_%3F%3F%3F%3F%3F%3F%3F%3F%3F-%3F%3F-%3F%3F%3F%3F%3F%3F-activity-6981596865775460352-kA-r/?utm_source=share&utm_medium=member_android) **。**

# 💸和我们一起做广告💸

**我们希望与来自世界各地的出色的 infosec、pen testing 和道德黑客团队、品牌和公司合作。如果这听起来像你，** [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) **与我们合作。**

**——————**

这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。

**在我们说再见之前……**

如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨

如果您有问题、意见或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。

下周再见。

很多爱

编辑团队，

[信息安全报道](https://infosecwriteups.com/)

*这份简讯是我们与“神奇大使”合作制作的。*

*资源贡献者:* [*阿尤什·辛格*](https://twitter.com/AyushSingh1098) *，* [*比马尔·k·萨胡*](https://twitter.com/srb1mal) *，* [*马尼凯什·辛格*](https://twitter.com/X71n0?t=WYKqmnE22AY_ZAq73FeCOA&s=09) *，* [*尼基尔·梅马内*](https://twitter.com/NikhilMemane09) *，* [*莫希特·赫姆昌达尼*](https://twitter.com/mohitkchandani)
*通迅格式由:* [*哈迪克辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，* [*维奈库马尔*](https://twitter.com/R007_BR34K3R) *，* [*西达尔特*](https://twitter.com/illucist_) *，* [*阿尤什辛格*](https://twitter.com/AyushSingh1098) *。*

## 每天，信息安全领域都会出现很多难以跟上的问题。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！