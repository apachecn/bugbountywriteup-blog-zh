# IW 周刊#13:暴露的 1000 个用户令牌、Oracle 中的预授权、AWS 错误配置、IDOR、开放重定向、Nmap 教程等等。

> 原文：<https://infosecwriteups.com/iw-weekly-13-1000s-of-user-tokens-exposed-pre-auth-rces-in-oracle-aws-misconfigurations-idor-46d1771fdbd6?source=collection_archive---------3----------------------->

![](img/d5bf539d57509357112b265ad95d10bd.png)

嘿👋

欢迎来到第十三期的**Infosec Weekly**——这是一份周一的时事通讯，将最好的 Infosec 信息直接发送到您的收件箱。

信息安全世界在不断发展，每天都有新的发现和功能出现。跟不上变化？别担心。在今天的版本中，我们以 5 篇文章、4 个线程、3 个视频、2 个 Github repos 和工具以及 1 个工作警报的形式，策划了所有需要您关注的令人惊叹的 Infosec 内容，以帮助您最大限度地从这份简讯中受益，并在您的职业生涯中向前迈出一大步。

听起来很有趣？让我们开始吧👇

**📝5 篇信息安全文章**

**#1** [**团队 Nautilus at Aquasec**](https://twitter.com/AquaSecTeam) **发现** [**数万个用户令牌通过 Travis CI API**](https://blog.aquasec.com/travis-ci-security) **暴露，这使得任何人都可以访问历史明文日志。**

**#2 一篇由**[**slow mist**](https://slowmist.medium.com/)**撰写的文章描述了** [**一个白帽组织，联合全球白厅安全团队(UGWST)如何能够发现浏览器扩展专用漏洞**](https://slowmist.medium.com/metamask-clickjacking-vulnerability-analysis-f3e7c22ff4d9) **，该漏洞允许攻击者欺骗用户发送加密资产，而用户却没有意识到这一点。**

**#3** [**彼得逊**](https://peterjson.medium.com/)**&**[**张**](https://testbnull.medium.com/) **最后在 Oracle 中间件内部的很多产品中有多个预授权。**

[**点击**](https://peterjson.medium.com/miracle-one-vulnerability-to-rule-them-all-c3aed9edeea2) **阅读更多关于这个很酷的漏洞利用故事。**

**#4**

#5 Portswigger 已经改进了 DOM invader 工具，使查找 C-SPP(客户端原型污染)变得像点击几次鼠标一样容易。

[**在这里了解一下**](https://portswigger.net/blog/finding-client-side-prototype-pollution-with-dom-invader) **。**

**🧵4 趋势螺纹**

**# 1**[**@ lazy sad**](https://twitter.com/LazySaad)**讲解了** [**如何找到 IDOR 以及我们如何使用 user_id 来验证 IDOR 是否存在**](https://twitter.com/LazySaad/status/1538664657749266433?s=20&t=FDeNgK3zKzJ4EhiTU8H3Pg) **。**

**# 2**[**@ tabaahi _**](https://twitter.com/tabaahi_)**解释了初学者如何寻找开放重定向，使用什么工具，如何着手寻找它们，以及应该在哪里报告开放重定向。** [**查看这里。是 Infosec**](https://twitter.com/tabaahi_/status/1539159408026218496?t=mF-ote2xiy0kFuvVgHjwTA&s=19) **新手的绝佳起点。**

**#3 一个来自**[**hos sein NafisiAsl**](https://twitter.com/MeAsHacker_HNA)**的伟大线索，他在这里解释了** [**一个 HTTP 请求走私如何变成大规模帐户接管，并分享了一个伟大的 GitHub 资源库，在那里他收集了惊人的评论&技巧**](https://twitter.com/MeAsHacker_HNA/status/1538862575617814528?t=YitdeDYSt2_fdJTWiK_qmA&s=19) **。**

**# 4**[**@ hakkluke**](https://twitter.com/hakluke)**[**30 秒内成为 Nmap 专业人员线程**](https://twitter.com/hakluke/status/1539962767901618176?t=2j3vJ9bWBKACz26tUAeLyw&s=19) **”展示了一个令人惊叹的 Nmap 完整教程，他在其中分享了您不想错过的所有 Nmap 特性。****

****📽️ 3 个有见地的视频****

****# 1**[**Rana Khalil**](https://www.youtube.com/c/ranakhalil101)**的下一个视频是为 Web Security Academy 的命令注入模块第二实验室准备的，** [**在那里，她使用 python 以手动和自动方式解决了同一个**](https://youtu.be/KbWn4L2dcHU) **。****

****#2 来自**[**Patrick Collins**](https://www.youtube.com/c/patrickcollins)**关于审计智能合同的一个很棒的视频，其中** [**他解释了审计流程，如何进行审计的基础知识，以及如何与审计师**](https://youtu.be/TmZ8gH-toX0) **互动。****

****#3** [**拉鲁卡**](https://twitter.com/TheLaluka) **聚集了许多游走于自然界的 rce，并决定作为拉鲁卡制作的 HitchHack 的一部分出席会议。** [**看这里的视频**](https://youtu.be/Z9GN6cuggYQ) **。****

****⚒️2 Github 库&工具****

****# 1**[**Xnl-h4ck 3r**](https://twitter.com/xnl_h4ck3r)**已经** [**推出了他们名为 xnLinkFinder 的 python 工具类似于他们的 Burp 扩展名为 GAP**](https://github.com/xnl-h4ck3r/xnLinkFinder) **。使用正则表达式有助于发现给定目标的端点。与一个名为**[**link finder**](https://github.com/GerbenJavado/LinkFinder)**的工具类似但更高级。****

****# 2**[**Razzorsec**](https://github.com/razzorsec)**在他们的 github repo 上分享的一份非常详细的以太坊智能合约审计师路线图。** [**在这里找到**](https://github.com/razzorsec/AuditorsRoadmap) **。****

****💰1 工作警报⚠️****

**Cybertix **为每一个想要专业经验和提升自我技能的人提供了一个绝佳的实习机会。****

**申请实习的最后日期:2022 年 7 月 15 日**

**这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。**

****在我们说再见之前……****

**如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨**

**如果您有问题、意见或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。**

**下周再见。**

**很多爱**

**编辑团队，**

**[信息安全报道](https://infosecwriteups.com/)**

***这份简讯是我们与“神奇大使”合作制作的。***

***资源贡献者:* [*尼辛 R*](https://twitter.com/thebinarybot)*(*[*thebotsite . me*](https://www.thebotsite.me/)*)，* [*阿尤什辛格*](https://twitter.com/AyushSingh1098) ， [*维奈库马尔*](https://twitter.com/R007_BR34K3R) *，* [*哈迪克辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，***

***通迅格式由:* [*尼辛 R*](https://twitter.com/thebinarybot)[*巴维亚贾因*](https://twitter.com/bhavyajain_30)[*维奈库马尔*](https://twitter.com/R007_BR34K3R)*[*西达尔特*](https://twitter.com/illucist_) *。****

***如果您希望加入我们的大使频道并为时事通讯投稿，请使用您的 discord 用户名回复此电子邮件。***