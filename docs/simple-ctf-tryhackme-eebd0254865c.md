# 简单的 CTF-特里哈克姆

> 原文：<https://infosecwriteups.com/simple-ctf-tryhackme-eebd0254865c?source=collection_archive---------0----------------------->

## 夺旗类游戏

欢迎神奇的黑客我想到了另一个很酷的文章，这是 Tryhackme 简单 CTF 写。

![](img/1e5c0cf259c722017e8ca1c2ee82f780.png)

别浪费时间了，让我们开始吧。部署后，我开始扫描目标。

![](img/1fab114d51125286209d813d0edc9b6a.png)![](img/41ad7789bbb7ba611bb38a5692f60c5a.png)

我在扫描目标时发现了一系列有用的信息。然后我使用 Gobuster 工具来查找有用的目录。

![](img/bad0d135340958e336ab0e806fe589ac.png)

从 Gobuster 我找到了/简单目录。

在查看了/simple 目录后，我发现这是一件有趣的事情！！！

![](img/3bf14edb162a25e73b5afbfcf46a7def.png)

名为 CMS 的网站很简单，我进一步检查了该版本是否在网站上公开，最终我找到了它。

![](img/4102b74a4cddd3c4be1ec1bd07720ebc.png)

我在谷歌上查了 CVE 的这个版本。

![](img/17a8c492075c6ae592b7db59d9aee3d1.png)

最后，我在 exploit-db.com 发现了这个版本的漏洞。

下载并执行了那个漏洞。

![](img/e0ee703b85e11175b0c451311722768e.png)![](img/25b07e227ff20719f8ecf96c0ceacdeb.png)![](img/cb877b8de5a2ab40f45189a9e2bad95c.png)

我们必须安装执行漏洞利用所需的库。

执行利用后，我发现密码和盐散列。

![](img/81215877d28ccf0a902f00811b17776e.png)![](img/14f67c9e749c5939c16dea27c65e9433.png)

我使用 Hashcat 工具来破解密码。

![](img/5cbd3e64b29a846f7d4d9252dd5a69a2.png)

最后，我发现了密码秘密。然后我用找到的密码通过 ssh 登录。

![](img/67170feaad446dec800f2c5f6b95503a.png)

然后我发现了一个标志 user.txt。

![](img/3abc24929feefe201dccdbee292de602.png)![](img/c1e0e5101b4b306e35a4835520f3d760.png)

最后，我们通过生成外壳找到了根标志。

![](img/b8580149a6e501e3171f5c01c3746ec3.png)![](img/58c563b1e5a014ef4a10e5e4402112b3.png)

**🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。** [**查看更多详情并在此注册。**](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)