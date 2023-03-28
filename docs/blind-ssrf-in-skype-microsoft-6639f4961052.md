# Skype 中的盲人 SSRF(微软)

> 原文：<https://infosecwriteups.com/blind-ssrf-in-skype-microsoft-6639f4961052?source=collection_archive---------0----------------------->

服务器端请求伪造是一个漏洞，使得攻击者能够向攻击者控制的网络位置/路径发出服务器请求。

在分析 Skype for Web 的 Burp 中的请求时，在*找到一个端点。*.skype.com/path？url=https://example.com，因为 url 参数看起来很有趣，所以我试图用我的 ngrok 实例来更改 URL &结果成功了！

通过查看 ngrok inspect web 控制台，验证收到的用户代理标题(Skype)和 who.is 中的 IP 地址，确认是 Skype 点击了 url。

虽然我能够让服务器点击任意网页，但我无法获得完整的响应。我只能从几个选定的 HTML 标签中获得状态代码、内容类型、响应的内容长度(大小)和文本内容。也就是说，它不是预期中的完整的 SSRF，而是一个盲目的/部分的 SSRF。

试图进入下面的路径—

1.  本地主机/内部 ip 地址->失败
2.  尝试使用 url 重定向/url 缩写方法绕过本地主机/内部 ip 地址->失败
3.  外部 ip 地址/网页->成功
4.  通用 Azure/AWS/DigitalOcean 元数据 IP 地址->失败
5.  不常用，Azure 相关的 IP 地址(168.63.129.16)->成功-->此 IP 可用于通过使用[http://168.63.129.16/metadata/v1/maintenance](http://168.63.129.16/metadata/v1/maintenance)端点来确定虚拟机的健康状况，如果虚拟机正在运行，该端点应返回 OK (200 状态代码)。(更多信息请参考[本](https://learn.microsoft.com/en-us/azure/virtual-network/what-is-ip-address-168-63-129-16?WT.mc_id=docs-azuredevtips-azureappsdev)

尝试将 url 参数值更改为 http://168.63.129.16/metadata/v1/maintenance 的，得到 200 Ok 响应，响应大小为 2 字节，这确认了响应文本可能包含 Ok 响应。

做了一份很好的报告，提到了所有的细节，然后坐等微软复制并修改报告。

幸运的是，这是在 M365 赏金计划的范围内，并且得到了一笔可观的赏金！

![](img/29333279843a710709e529802718fc29.png)

尽情狂欢吧！

报告时间表:

1.  已报告—2022 年 9 月 23 日
2.  更多详细信息已更新—2022 年 10 月 3 日
3.  赏金奖励—2022 年 10 月 8 日
4.  固定—2022 年 10 月 12 日

# 喜欢我的文章吗？**在 Twitter(**[**)@ jayaterthag**](https://twitter.com/jayateerthaG)**)和 medium 上关注我，了解更多关于 bugbounty、Infosec、网络安全和黑客攻击的内容。**

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 条线索、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！