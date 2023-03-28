# 我是如何赢得我的第一笔昆虫赏金 1000 美元的

> 原文：<https://infosecwriteups.com/how-i-earned-my-first-bug-bounty-reward-of-1000-9dc6643977e4?source=collection_archive---------0----------------------->

## 在这篇文章中，我想讨论一下我从 Bug Bounty 计划中赚 1000 美元的旅程，以及我从这个旅程中学到的教训，这些教训可以帮助其他以成为一名成功的 Bug 猎人为目标的人。

![](img/955f55975e8ecdf04b480d52b819c3c0.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 拍摄的照片

> 在你继续之前，请投票表决你的意见。这将有助于我计划接下来的步骤。有关该计划的更多信息，请访问此博客:
> 
> 阅读:[寻求您的输入。这不是博客。这是一项民意调查](https://medium.com/@praveenjalasutram/seeking-your-input-this-is-not-a-blog-its-an-opinion-poll-463fb8d20448)。
> 
> 提前感谢。感谢你的时间

获得第一笔 1000 美元的 bug 奖金是我作为黑客和安全研究员职业生涯中的一个重要里程碑。这是一次既有挑战又有收获的经历，让我学到了关于 bug 赏金猎人世界的宝贵经验，以及及时了解最新漏洞和利用的重要性。

在我深入研究我如何获得我的第一个虫子赏金的细节之前，提供一些关于什么是虫子赏金猎人和它如何工作的背景是很重要的。从本质上讲，bug 赏金是一家公司或组织为发现并报告其系统或软件中的漏洞而提供的奖励。这些漏洞也称为“bug”，范围从相对较小的问题到可能被黑客利用的严重安全缺陷。

Bug bounty 程序正变得越来越受欢迎，作为公司众包其安全测试并确保其系统尽可能安全的一种方式。许多大型科技公司都有漏洞奖励计划，越来越多的小公司和组织也是如此。

作为一名 bug 赏金猎人，我的目标是找到并报告尽可能多的漏洞，以便获得奖励。回报可能因漏洞的严重性和影响以及发现和利用漏洞的难度而异。

# 我的第一个奋斗目标:研究

那么，我是如何获得第一笔 1 万美元的昆虫奖金的呢？这一切都始于大量的研究和实践。在我开始积极搜索漏洞之前，我花了无数时间学习不同的攻击技术，研究软件漏洞，并磨练我作为黑客的技能。

> 提示:您做的研究越多，就越容易发现漏洞

在这个过程中，我使用的最有价值的资源之一是开放 web 应用程序安全项目(OWASP)，这是一个非营利性组织，提供了大量关于 Web 应用程序安全的信息。我还参加了在线黑客挑战和 CTF(夺旗)活动，这些活动帮助我了解了不同的漏洞以及如何利用它们。

一旦我对自己的能力有了信心，我就开始积极寻找各种系统和软件的漏洞。这涉及到大量的试验和错误，以及大量的耐心。在我最终发现一个公司愿意付钱给我修复的漏洞之前，我不得不筛选许多错误的线索和死胡同。

# 赚钱

我发现的漏洞是机密公司的一个网络应用程序中的一个**跨站点脚本(XSS)** 缺陷。XSS 漏洞允许攻击者向网站注入恶意代码，然后不知情的用户可以执行这些代码。在这种情况下，我发现的漏洞允许我向 web 应用程序中注入恶意代码，这些代码可能被用来窃取用户的敏感信息。

对于那些不知道的人:

```
What is Cross-Site Scripting according to OWASP:

Cross-Site Scripting (XSS) attacks are a type of injection, 
in which malicious scripts are injected into otherwise benign 
and trusted websites. XSS attacks occur when an attacker uses 
a web application to send malicious code, generally in the form
of a browser side script, to a different end user. 

Flaws that allow these attacks to succeed are quite widespread 
and occur anywhere a web application uses input from a user within
 the output it generates without validating or encoding it.

An attacker can use XSS to send a malicious script to an unsuspecting 
user. The end user’s browser has no way to know that the script should 
not be trusted, and will execute the script. Because it thinks the script 
came from a trusted source, the malicious script can access any cookies, 
session tokens, or other sensitive information retained by the browser 
and used with that site. These scripts can even rewrite the content of 
the HTML page.
```

一旦我发现了这个漏洞，我就通过他们的漏洞奖励计划向<confidential>公司报告。报告漏洞的过程简单明了，并且有详细的文档记录。我提供了该漏洞的详细描述，以及一篇演示如何利用该漏洞的概念验证文章。由于该网站不在著名的 bug 赏金平台如 [HackerOne](https://www.hackerone.com/product/bug-bounty-program) 或 [BugCrowd](https://www.bugcrowd.com/products/bug-bounty/) 中，我不得不通过公司安全团队的官方电子邮件提交我的证明。</confidential>

提交报告后，我耐心地等待答复。该公司花了几周时间审查我的报告，并确认该漏洞确实有效。这对我来说是一个灵光乍现的时刻，因为我没有想到公司的安全运营团队会有这样的反应。一旦他们确认了这个漏洞，他们就奖励我 1000 美元。

> [不要迷失在黑暗中，保持领先:点击这里加入我的社区，学习真正的网络安全技能！](https://praveenjalasutram.medium.com/subscribe)

# 连续成功

获得我的第一笔昆虫奖金对我来说是一个巨大的成就，知道我的努力和奉献得到了回报是一种很好的感觉。除了经济回报，我还获得了关于 bug 赏金猎人和 web 应用程序安全的宝贵经验和知识。

总的来说，获得我的第一笔昆虫奖金是一次富有挑战性但却值得的经历。它教会了我了解最新漏洞和利用的重要性，并给了我继续搜索漏洞和获得奖励所需的信心和技能。

自从获得第一笔 bug 赏金以来，我一直参与 bug 赏金计划，发现并报告了各种系统和软件中的大量漏洞。我也学到了很多关于 bug bounty 行业的知识，以及合乎道德的黑客行为和负责任的披露的重要性。

# 经验教训

> 我学到的一个重要经验是，遵守每个臭虫奖励计划的规则和指导方针是非常重要的。

许多公司对如何报告和利用漏洞都有严格的规定，违反这些规定可能会被取消项目资格，甚至面临法律后果。在参与之前，仔细阅读和理解每个 bug bounty 计划的条款和条件是很重要的。

我学到的另一个教训是耐心和坚持的重要性。发现和报告漏洞可能是一个耗时且令人沮丧的过程，即使事情不尽如人意，保持专注和不断尝试也很重要。发现一个漏洞可能需要几个小时甚至几天的时间，而且经历一段时间没有发现任何东西的干旱期并不罕见。重要的是不断尝试，不要气馁。

最后，我认识到了及时了解最新漏洞和利用的重要性。黑客和安全领域在不断发展，了解最新的技术和工具非常重要。这可能包括阅读行业博客和论坛、参与在线社区以及参加会议和活动。

# 结论

总之，赢得我的第一笔 10，000 美元奖金是我作为一名黑客和安全研究员职业生涯中的一个重要里程碑。这是一次富有挑战性但却值得的经历，它让我学到了关于 bug 赏金猎人世界的宝贵经验，以及及时了解最新漏洞和利用的重要性。这些经验帮助我继续发现和报告漏洞并获得回报，我希望在未来的许多年里继续为安全领域做出贡献。

> 喜欢我的作品吗？那你为什么不支持我:
> 
> 给我买杯咖啡！

> [不要迷失在黑暗中，保持领先:点击这里加入我的社区，学习真正的网络安全技能！](https://praveenjalasutram.medium.com/subscribe)

# 同样来自作者:

*   [如何在暗网上找到被攻破的凭证？](/how-to-find-compromised-credentials-on-darkweb-6e2af2b3a0e8)
*   8 个免费网站可以检查你的电子邮件地址是否被泄露？
*   [使用 Python 和 Tor 创建暗网爬虫](/creating-darkweb-crawler-using-python-and-tor-53169d146301)
*   [利用 ChatGPT 创建暗网监控工具](/using-chatgpt-to-create-darkweb-monitoring-tool-7b7eeaab351f)
*   你知道暗网有自己的法庭和司法系统吗？
*   [利用这些表面网络资源探索暗网:暗网洋葱链接的大集合](/explore-darkweb-with-these-surface-web-resources-a-large-collection-of-darkweb-onion-links-92a426f9c0f9)
*   [我是如何赢得我的第一笔 1000 美元 Bug 赏金的](/how-i-earned-my-first-bug-bounty-reward-of-1000-9dc6643977e4)
*   [如何随着时间的推移提高你的 Bug 赏金表现？](/how-to-improve-your-bug-bounty-performance-over-time-5f4ace641db0)
*   [TOR 能让你匿名吗？查看 FBI 如何逮捕一名非法 TOR 用户](https://medium.com/coinmonks/can-tor-keep-you-anonymous-see-how-fbi-arrested-an-illegal-tor-user-ef8288f3480e)
*   [不要被抓！Bug 赏金猎人该不该用 VPN？](https://medium.com/coinmonks/dont-get-arrested-should-you-use-vpn-for-bug-bounty-hunting-c39019f34f10)
*   [俄罗斯、中国、美国、乌克兰——地缘政治对你们的网络威胁情报战略意味着什么？](https://praveenjalasutram.medium.com/russia-china-us-ukraine-what-does-geopolitics-mean-to-your-cyber-threat-intelligence-strategy-e9edf206de60)
*   网络威胁情报不仅仅是威胁的指标。事实核查！
*   [评估网络威胁的艺术:如何识别和减轻真正的风险](https://medium.com/@praveenjalasutram/the-art-of-assessing-cyber-threats-how-to-identify-and-mitigate-real-risks-as-a-pro-66fd491300a5)
*   [使用这个免费工具评估您的网络安全计划的成熟度](https://medium.com/bugbountywriteup/assess-maturity-of-your-cyber-security-program-with-this-free-tool-371c69a624ec?source=your_stories_page-------------------------------------)
*   [风险与威胁:你在安全策略中犯下的致命错误](https://medium.com/bugbountywriteup/risk-vs-threat-the-fatal-mistake-youre-making-in-your-security-strategies-978b142006a?source=your_stories_page-------------------------------------)
*   [LockBit 勒索软件隐藏的秘密被揭露！！！](https://medium.com/coinmonks/hidden-secrets-of-lockbit-ransomware-revealed-538b85296afc)
*   [了解你的对手:古巴勒索软件](/know-your-adversary-cuba-ransomware-7b899be0410d)
*   [勒索软件谈判:该做什么不该做什么](https://praveenjalasutram.medium.com/ransomware-negotiations-dos-and-don-ts-5f89883be705)
*   [十大活跃勒索软件团伙:地缘政治、起源和目标](https://medium.com/coinmonks/top-10-active-ransomware-gangs-geopolitics-origin-and-targets-a8238ed1c098)
*   [超越黑暗网络:Telegram 成为威胁者的新枢纽](https://medium.com/coinmonks/beyond-dark-web-telegram-emerges-as-the-new-hub-for-threat-actors-6166f8e1d82)
*   [ChatGPT 瘾:ChatGPT 会让你痴迷的 3 个理由！](https://medium.com/coinmonks/the-chatgpt-addiction-3-reasons-why-chatgpt-will-make-you-obsessed-65a30a515e7c)
*   [我的文章如何在搜索引擎优化的 Google #1 页面上排名](https://medium.com/illumination/how-my-article-ranked-on-google-1-page-with-seo-a4d0fa569401)
*   [你不会相信这个 AI 工具是如何分分钟建好一个网站的！](https://praveenjalasutram.medium.com/you-wont-believe-how-this-ai-tool-can-build-a-website-in-minutes-ea0ad7870bf1)
*   [如何成功获得 Bug 赏金？](https://medium.com/@praveenjalasutram/how-to-succeed-in-bug-bounty-9b9ff5d0542f)
*   成功实施臭虫奖励计划的七大秘诀
*   [如何获得网络安全方面的工作？](https://medium.com/illuminations-mirror/how-to-get-a-job-in-cybersecurity-cdf6ec46d542)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！