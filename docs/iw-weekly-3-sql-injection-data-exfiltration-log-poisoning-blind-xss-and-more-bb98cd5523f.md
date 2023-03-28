# IW 周刊#3: SQL 注入，数据泄漏，日志中毒，盲目 XSS，等等。

> 原文：<https://infosecwriteups.com/iw-weekly-3-sql-injection-data-exfiltration-log-poisoning-blind-xss-and-more-bb98cd5523f?source=collection_archive---------1----------------------->

![](img/81892102dc142e4c6b30dc36379c9586.png)

嘿👋

欢迎来到第三期的 **Infosec Weekly** —这是一份周一的时事通讯，将 Infosec 的最佳报道直接发送到您的收件箱。

希望你这周过得愉快。我们的团队很高兴地宣布，我们的媒体出版物 [Infosec Writeups，](https://infosecwriteups.com/)已经拥有 25，000 多名读者。我们的 [LinkedIn 页面](https://www.linkedin.com/company/infosec-writeups)也有超过 10，000 名粉丝。如果你已经在这个刊物上发表了一篇文章，可以随时在你的 LinkedIn 个人资料上添加你自己为作家，以获得额外的收入🔥

在今天的简讯中，我们整理了一些精彩的文章，帮助您了解 Infosec 中的一些新内容，并打破常规进行思考。

激动吗？让我们来看看我们团队精心挑选的一些有趣的报道👇

**#1 —** [**了解如何滥用 svg foreignObject 来获取内部信息，使用 javascript 来过滤数据，以及使用 meta 和 style 标签绕过其他一些方法。**](/svg-ssrfs-and-saga-of-bypasses-777e035a17a7)

**#2 —** [**了解如何在 macbook 中使用 android studio 设置 burp，以便测试 android 应用程序。**](/android-pentesting-setup-on-macbook-m1-d2f1f0a8db4b)

**#3 —** [**了解如何将 youtube 视频中的 url 升级为 SQL 注入。**](/how-a-youtube-video-lead-to-pwning-a-web-application-via-sql-injection-worth-4324-bounty-285f0a9b9f6c)

**#4 —** [**了解如何通过在用户代理标头中发送恶意负载来毒害日志，从而获得盲 XSS。**](/log-poisoning-inject-payloads-in-logs-e7f1fa338f2f)

**#5 —** [**了解如何使用 google dorks 查找漏洞。**](/my-first-reflected-xss-bug-bounty-google-dork-xxx-92ac1180e0d0)

**#6 —** [**了解如何使用 chrome 调试器对 javascript 文件进行逆向工程，以绕过客户端游戏。**](/hackeando-wordle-7d4ca6a9fa90)

这星期就这些了。希望你喜欢这些令人难以置信的发现，并从今天的时事通讯中学到一些新东西。

**本周视频**

我们最近组织了 IWCON 2022:令人敬畏的虚拟网络安全会议和网络活动。如果你错过了它，或者你想再次见证它的精彩，我们每周一为你带来两个视频:

1.  Anugrah SR 分享了他从生物学家到安全顾问的转变历程。[看这里](https://www.youtube.com/watch?v=Nn2PNouH-Z8)。
2.  [Roshan Piyush](https://twitter.com/roshanpiyush) 谈到了向易受攻击的服务发射 API 箭的剖析技术。[看这里](https://youtu.be/1P0Ia3zar3s)。

**说到这里，**我们计划很快推出 IWCON 超级版。现在，您甚至可以在全世界见证之前，成为令人敬畏的虚拟网络安全事件的一部分！[填写此表格](https://docs.google.com/forms/d/11cRDhnD-6Q80sb_36Ybx7yQt6_zf467xTCT4mXTt0Ts/edit)帮助我们帮助“您”从世界各地的专家那里获得最佳的信息安全见解和知识。😊

**在我们说再见之前……**

如果您觉得这份简讯有用且有趣，并且知道其他人也会觉得有趣，如果您能将它转寄给他们，我们将不胜感激📨

如果您有问题、评论或反馈，请回复此邮件或在 Twitter [@InfoSecComm](https://twitter.com/InfoSecComm) 上告诉我们。

下周再见。

很多爱

编辑团队，

[信息安全报道](https://infosecwriteups.com/)