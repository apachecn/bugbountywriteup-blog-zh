# 我第一次 XSS 的故事

> 原文：<https://infosecwriteups.com/tale-of-my-first-xss-27f622bc47c0?source=collection_archive---------1----------------------->

大家好，所以我想以一个问题开始这篇博客，当你开始进入网络安全领域时，你学到的第一个漏洞是什么？？

我的是 XSS，虽然我花了很长时间才找到它，但是耶！我终于找到了\_(ツ)_/

# 个案研究

我收到了 HackerOne 上一个私人项目的邀请。它几乎有 600 多份报告，而只有 4 项资产。该死的，我能找到什么吗？？我还是开始了正常的侦察，开始玩应用程序。这里运气不好！

现在，我开始挖掘参数。**如何？**给你

[](https://github.com/devanshbatham/ParamSpider) [## devanshbatham/ParamSpider

### 从输入域的 web 档案中查找参数。也从子域中查找参数。给予…支持

github.com](https://github.com/devanshbatham/ParamSpider) [](https://github.com/s0md3v/Arjun) [## s0md3v/Arjun

### HTTP 参数发现套件 Web 应用程序使用参数(或查询)来接受用户输入，如下…

github.com](https://github.com/s0md3v/Arjun) [](https://github.com/hakluke/hakrawler) [## 哈克卢克/哈克劳勒

### hakrawler 是一个 Go web crawler，旨在方便、快速地发现 web 应用程序中的端点和资产。

github.com](https://github.com/hakluke/hakrawler) 

您也可以使用“Waybackurls | gau”来查找参数。这些是我迄今为止用过的一些很棒的技巧，结果非常惊人，\_(ツ)_/

有了所有的参数列表，我开始模糊地想着，我会得到一些有趣的东西*(积极的共鸣)*，瞧！！我发现一个没有很多过滤器的参数。首先，我把它发送到一些 XSS 扫描工具，因为这是我第一次运行 XSS。

[](https://github.com/s0md3v/XSStrike) [## s0md3v/XSStrike

### 最先进的 XSS 扫描仪。在 GitHub 上创建一个帐户，为 s0md3v/XSStrike 开发做贡献。

github.com](https://github.com/s0md3v/XSStrike) [](https://github.com/hahwul/dalfox) [## 哈哈哈/达尔福克斯

### 只是，XSS 的扫描和参数分析工具。我以前开发过 XSpear，一个基于 ruby 的 XSS 工具，而这次，一个…

github.com](https://github.com/hahwul/dalfox) 

我得到了积极的结果，我发现 csrfToken 就在那里等着我

```
https://sub.example.com/?csrfToken=htmhunter
```

所以我开始探索如何获得弹出窗口

那天晚些时候。我在 HackerOne 上报道了我的第一个 XSS。

![](img/31c818ebc372961d0f7ce6afaf4503f6.png)

**外卖—**

“老节目，很多报道，很多这样的事情”不要让它困扰你。总是试图找到越来越多的参数，模糊他们所有:D

这个博客到此为止。希望你喜欢。

在 [LinkedIn](https://www.linkedin.com/in/adtyasoni) 、 [Twitter](https://twitter.com/hetroublemakr) 上与我联系

如果你喜欢这个博客，请点击👏按钮，并分享它以帮助其他人找到它。