# 如何解决布鲁克林九九 TryHackme 演练

> 原文：<https://infosecwriteups.com/how-to-solve-brooklyn-nine-nine-tryhackme-walkthrough-b0a6e4e2e534?source=collection_archive---------1----------------------->

## CTF 之旅

欢迎回来神奇黑客我在 Tryhackme(布鲁克林九九)上想出了另一个好玩有趣的话题演练。

![](img/2eab64c678c37051f7d86883dd9754bc.png)

让我们进入主题，部署后，我像往常一样开始扫描目标，寻找任何有用的信息。

![](img/1fab114d51125286209d813d0edc9b6a.png)

我扫描目标时发现 FTP 端口打开了。所以我尝试了 FTP 匿名登录。

![](img/e5bb6ee1900f191de3d54ec88fa80fa5.png)

我试着用 FTP 登录，成功了。

![](img/4936204d2710431e47723653149edf74.png)

我查看了 FTP 接入的文本文件。

![](img/bcd74bb1cceaa0f921c9faf186001d8a.png)

我用九头蛇蛮力破解了密码。我用 ssh 登录了它。

![](img/03c4487e9f9765afff45dd504f9506b6.png)![](img/e1d91ccaac95f77ac4521637d8193788.png)

在我导航到 holt 目录后，我发现了一个用户标志。

![](img/dd0c39431c548e197a3d4530188b7f72.png)

接下来，我开始寻找根旗。

![](img/cae2acfbb62f2a31438f893fa94dfadb.png)

我键入 sudo -l 进行 root 登录访问。

![](img/fdc89dd29585eec2a39ec53bb9d61b5b.png)

终于懂了。

![](img/cf7e3c3d61ae3bc5440dd8d98e357897.png)

🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。 [**查看更多详情并在此注册。**](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)