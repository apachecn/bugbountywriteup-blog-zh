# API 黑客秘密第 3 部分

> 原文：<https://infosecwriteups.com/api-hacking-secrets-part-3-3872f98eff73?source=collection_archive---------2----------------------->

![](img/b647aeed6d3be11e6e18eea303a16f44.png)

**查找 WSDL 文件提取端点**

API 中最重要的东西是端点，大多数时候你只是玩玩端点来寻找漏洞。因此，在这一部分中，我们将学习如何从 WSDL 文件中提取端点。

你需要做的第一件事是你需要做 recon，API recon 是最简单的，你只需要找到文档和一些谷歌搜索，你的任务就完成了。

**侦察:**

假设你有一个目标网站【example.com 。

如上所述，我们需要找到 WSDL 文件，这样我们就可以从中提取端点。还有更多方法可以找到端点，我将在后面的部分介绍，现在，让我们只专注于找到 WSDL 文件。

如果你运气好的话，只需添加**就能获得 WSDL 文件？WSDL** 在基础 API 的最后。从上述 example.com 的例子中，考虑到在网站 example.com 后面运行的 API 服务是 api.examle.com 的，那么你可以很容易地找到 wsdl 文件，只需添加

[https://api.example.com/api/**？wsdl**](https://api.example.com/api/?wsdl)

> 你也可以通过谷歌搜索得到类似的结果，即[**www.example.com**](http://www.example.com)**文件类型:WSDL**
> 
> **站点:target.com 文件类型:wsdl**
> 
> **ext:svc inurl:wsdl**
> 
> **文件类型:wsdl wsdl**
> 
> **文件类型:？wsdl**
> 
> **·inurl:asmx？wsdl 还是 inurl:jws？wsdl**
> 
> **·inurl:_ VTI _ bin/sites . asmx？wsdl | intitle:_ VTI _ bin/sites . asmx？wsdl**

如果这两种技术都不能给你带来结果，那么你一定要借助史上最受欢迎的工具 Burpsuite。

与打嗝套件，你需要一个附加的 WSDL 向导，它会自动从抓取的网址找到 WSDL 文件。adon 的链接在下面。

[](https://portswigger.net/bappstore/ef2f3f1a593d417987bb2ddded760aee) [## WSDL 巫师

### 这个扩展扫描 WSDL 文件的目标服务器。在执行应用程序内容的正常映射后，对…

portswigger.net](https://portswigger.net/bappstore/ef2f3f1a593d417987bb2ddded760aee) 

如果现在你也找不到 WSDL 的文件，有两种可能

1.  WSDL 文件不存在，已被网站所有者删除
2.  WSDL 文件不存在于该特定端点

除了这两个，还有一个场景是负责任的，如果你找不到 WSDL 的文件，你知道是什么吗？

**网站没有使用 SOAP api。**

我将请求我所有的读者，如果你知道一些更多的技术来找到 WSDL，请在评论部分回复，这样我们就可以学习更多的技术来找到 WSDL 文件，除此之外，如果你遇到任何谷歌呆子，可以帮助找到 WSDL 文件，请分享。

视频演示上述技术可以在我的 youtube 频道 API hacking 播放列表中找到

[**https://www.youtube.com/watch?v=Nd-cFZ_0-fU**](https://www.youtube.com/watch?v=Nd-cFZ_0-fU)

**查看下一部分了解更多细节…**