# 👩‍💻IW 周刊#34:攻击 SAML 2.0，Kubernetes 安全，RCE，黑客文件上传，侦查工具和方法，以及更多…

> 原文：<https://infosecwriteups.com/iw-weekly-34-attacking-saml-2-0-5d6cd7507ac4?source=collection_archive---------3----------------------->

![](img/0c53d244a3715fe39ca6d6f8d1cf3b9f.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[**@ redhuntlab**](https://redhuntlabs.com/blog)**对约 40000 个 Firebase 子域进行了大规模扫描，了解其安全状态。** [**在这里阅读他们的发现**](https://redhuntlabs.com/blog/analysing-misconfigured-firebase-apps-a-tale-of-unearthing-data-breaches-wave-10.html) **。**

嘿👋

欢迎来到#IWWeekly34，这是一份每周一期的时事通讯，将信息安全的精华直接发送到您的收件箱。

本周 NL 由 [**零点安全**](https://bit.ly/3AtEtHe) 为您带来。

这是我们赞助商的一句话:

“我们通过以可扩展的在线形式提供高质量的培训材料和实验室环境，使 Red Teaming 知识和技能组合更容易获得、更经济实惠，从而使企业和行业能够提高其网络防御能力和对抗恢复能力。 [**点击这里**](https://bit.ly/3AtEtHe) 了解更多关于我们的培训。”

回到今天的 NL，这里是我们本周的首选:7 篇文章，6 个线程，5 个视频，2 个 GitHub repos 和工具，1 个工作提醒，帮助你最大限度地从这份简讯中受益，并在你的职业生涯中向前迈出一大步。

激动吗？让我们开始吧👇

# 📝7 篇 Infosec 文章(5+ 2 初学者友好型)

**#1 SAML 已经过时。但是我们仍然在大型应用程序产品中发现 SAML 漏洞。下面由**[**@ paper _ see bug**](https://paper.seebug.org/)**详细研究** [**如何攻击 SAML 2.0 安全**](https://paper.seebug.org/2013/) **。**

**#2 继第 1 部分之后，**[**@ jack _ halon**](https://twitter.com/jack_halon)**又来了一个关于 Chrome 浏览器开发的** [**第 2 部分**](https://jhalon.github.io/chrome-browser-exploitation-2/) **其中讨论了 V8 的字节码、代码编译、代码优化等话题。**

**# 3**[**@ redhuntlab**](https://redhuntlabs.com/blog)**最近对大约 40000 个 Firebase 子域名的样本进行了大规模扫描，以了解它们的安全状态。阅读关于** [**他们从关于数据泄露**](https://redhuntlabs.com/blog/analysing-misconfigured-firebase-apps-a-tale-of-unearthing-data-breaches-wave-10.html) **的扫描中发现的问题。**

**#4 详细介绍了**[**@ Bipin Ji tiya**](https://twitter.com/win3zz)**如何利用** [**远程命令执行(RCE)借助漏洞链**](https://medium.com/@win3zz/remote-command-execution-in-a-bank-server-b213f9f42afe) **。**

**# 5 Infosec Twitter 上的每个人似乎都在跳槽去 Infosec.exchange Mastodon 服务器。以下是如何使用 HTML 注入在 Infosec Mastodon 上**[**@ garethheyes**](https://twitter.com/garethheyes)**窃取凭证，而无需绕过 CSP** **。**

## 初学者友好的-

**# 1**[**@ Edward Litchner**](https://zerodayhacker.com/author/hjdbvet6z3k/)**股份** [**如何才能模糊一个已签名的 JWT 来获取其加密密码**](https://zerodayhacker.com/hacking-a-jwt-json-web-token-part-1/) **。**

**# 2**[**@ agent 47 _ 2458**](https://hacklido.com/u/Agent47_2458)**在此详细分享他的** [**侦查工具和方法论**](https://hacklido.com/d/82-my-recon-tools-and-methodology) **。**

# 🧵6 趋势线程(4 + 2 初学者友好型)

**# 1 API 无处不在，供应用程序通信，但要看** [**你怎么黑掉它们**](https://twitter.com/intigriti/status/1592135581479632899?t=9VB_yt3skzhcKfw_b75SKw&s=19) **，参考**[**@ Intigriti**](https://twitter.com/intigriti/)**这个伟大的线程。**

**#2 阅读这篇有趣的帖子作者:**[**@ AppSecEngineer**](https://twitter.com/AppSecEngineer/)**让你了解 kubernetes 安全的基础知识:** [**所有关于 K8s 授权(AuthZ)**](https://twitter.com/AppSecEngineer/status/1592193199614869507?t=vklQZPpWYFW2Un9C61LzCQ&s=19) **。**

**# 3**[**@ Maik Ro**](https://twitter.com/maikroservice/)**分享了 SIEM 系列的另一个详细线索，其中他展示了** [**如何为您的麋鹿 SIEM**](https://twitter.com/maikroservice/status/1594109943749435392?t=1C80hEI1dHtFZq_umtfSew&s=19) **构建定制的 Kibana 小部件。**

**#4 要不要** [**hack 文件上传功能**](https://twitter.com/Steiner254/status/1594200872296366081) **？看@Steiner245 的这个帖子。**

## 初学者友好的-

**# 1**[**@ re cone**](https://twitter.com/ReconOne_bk/)**分享关于使用** [**GF 工具避免键入常见、复杂和长模式的最佳实践。**](https://twitter.com/ReconOne_bk/status/1592133502115680256?t=L-z6AImSeqli63DY3qwlRw&s=19)

**# 2**[**@ Intigriti**](https://twitter.com/intigriti/)**通过每个黑客都应该知道的至关重要的 9 个呆瓜技巧** **来讲述 Google 呆瓜的** [**威力。**](https://twitter.com/intigriti/status/1592497514766163968?t=54cMZpkOmFgUup2pqmE9XQ&s=19)

# 📽️ 5 个有见地的视频(3 + 2 初学者友好)

**#1 观看此视频了解** [**什么是 IPFS，攻击者如何利用**](https://youtu.be/iraMomr76Rs) **it 通过**[**@ dafthack**](https://twitter.com/dafthack)**传递恶意软件。**

**# 2**[**@ NahamSec**](https://twitter.com/NahamSec)**带你经历 Snyk CTF** **的** [**挑战之一。**](https://youtu.be/QLFrgNgVG2o)

**#3** [**Azure 后门:如何隐藏它们，如何找到它们**](https://youtu.be/IUcubSMkjNE) **，一个由** [**主讲的@_wald0**](https://twitter.com/_wald0) **。**

## 初学者友好的-

**# 1**[**@ Farah _ Hawaa**](https://twitter.com/Farah_Hawaa)**分享一些** [**学习安全代码复习的绝佳资源**](https://youtu.be/ajcxjnTFo6A) **。**

对于新手和一些经验丰富的猎人来说，在 infosec Twitter 上流传的 2 号赏金帖子可能会让人不知所措。[**@ gregxsunday**](https://twitter.com/gregxsunday)**分享他的** [**关于追求全职 bug 赏金猎人的跌宕起伏的旅程**](https://www.youtube.com/watch?v=q9rX5ty3fWI) **。**

# 新⚒️协议 GitHub 库和工具

**#1** [**一款检测 MitM 攻击的工具**](https://github.com/arijitdirghanji/MITM-Detection-and-Preventions) **由**[**@ ari JIT _ Dir**](https://twitter.com/Arijit_Dir)**。**

**#2** [**Csprecon 是由**](https://github.com/edoardottt/csprecon)[**@ edoardottt 2**](https://twitter.com/edoardottt2)**使用内容安全策略为目标发现新域** **的工具。**

# 💰1 个工作警报

**#1 Payatu 正在进行一场招聘马拉松，有 20 多个安全职位空缺。**

[**此处适用**](https://www.linkedin.com/posts/payatu_hiring-activity-6999280081785270272-xzbI?utm_source=share&utm_medium=member_desktop) **。**

# 💸和我们一起做广告💸

我们希望与来自世界各地的出色的 infosec、pen testing 和道德黑客团队、品牌和公司合作。如果这听起来像你， [**点击这里**](https://docs.google.com/forms/d/e/1FAIpQLSfb_v6aVoJUpKBcrEV7HgoZ8FL20QWUFDTWTkxZjQHp5UEhiA/viewform) **与我们合作。**

这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。

## **在我们说再见之前……**

如果你觉得这篇时事通讯很有趣，并且知道其他人也会感兴趣，如果你能把它转发给他们，我们将不胜感激📨

如果您有问题、评论或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。

下周再见。

很多爱

编辑团队，

[Infosec 报道](https://infosecwriteups.com/)

*这份时事通讯是我们与“神奇大使”合作制作的。*

*资源贡献者:* [*阿尤什·辛格*](https://twitter.com/AyushSingh1098) *，* [*哈迪克·辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，* [*普拉莫德·库马尔·普拉丹*](https://twitter.com/NinjaFurrry?t=AYh1WBkceSJD-UvG2qETTg&s=09) *，* [*尼克希尔·梅马内，*](https://twitter.com/NikhilMemane09) [*马尼凯什·辛格*](https://twitter.com/X71n0)

*通迅格式由:* [*哈迪克·辛格*](https://twitter.com/Kxddah?t=_Ghby7u5rNBfUxzzjEZUUw&s=09) *，* [*维奈·库马尔*](https://twitter.com/R007_BR34K3R) *，* [*西达尔特*](https://twitter.com/illucist_) *和* [阿尤什·辛格](https://twitter.com/AyushSingh1098) *。*

## [点击此处订阅时事通讯](https://weekly.infosecwriteups.com/)每周一将惊人的 Infosec 见解直接发送到您的收件箱。