# Gallery Tryhackme 演练第 1 部分

> 原文：<https://infosecwriteups.com/gallery-tryhackme-walkthrough-part-1-21cdf044b3b8?source=collection_archive---------2----------------------->

## **文件上传攻击**

久违了，欢迎回来，让我们重新开始道德黑客部分。

*酷让我们开始*

![](img/ad5536639e19ef1a90fb77a9749ce6ca.png)

照片由[克拉克·蒂布斯](https://unsplash.com/@clarktibbs?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

首先，我部署它，并开始扫描目标。

![](img/6e8fd5dcd84c582f394d0c47e0044bcb.png)

打开了两个端口，一个是 8080，另一个是 80。

端口 8080 是有趣的部分。

![](img/04a4f7caa3f70ddbde61b804992fe73c.png)

它有一个名为“简单图像图库系统”的登录界面。

你可以通过 SQL 注入进入目标网页界面。

![](img/29d6367f9db883655988cb0e1bd61185.png)

成功登录 web 应用程序后，导航到此位置，使用 Burpsuite 捕获请求，并将其发送到中继器，然后将请求另存为”。req”。并对我们保存的请求执行 SQL 注入，以获取管理员密码或哈希。

![](img/fedae347c5bed86d551e2cc695fde295.png)

开始键入以下命令获取数据库

> sqlmap -r test.req - dbs

![](img/8574957952b75334de0fc0d435a317b0.png)

找到的可用数据库是 gallery_db 和 information_schema。

然后我通过使用

![](img/2ae29cdc6e1533aa760e949fcd4b1dea.png)

> sqlmap-r test . req-D gallery _ D B- tables

输入这个命令后，我使用相册列表、图像、system_info 和用户等信息。

![](img/de0b1149c087b4203ecb05f5f56ec25c.png)

> 在我进入用户的表之后

![](img/1d92f30b1bc8f95f7a88f1e76a61ed10.png)

> sqlmap -r test.req -D gallery_db -T 用户-列

![](img/e410cf9fdc0352b40aa9c6208af73256.png)

我们的用户名和密码栏，我们试图进入它。

![](img/9a9deb5b7a6ca71fefb679877ff84128.png)

> sqlmap-r test . req-D gallery _ D B- T users-C 用户名和密码转储

![](img/46f3263ea915cf3437873e4ebc3ae6b3.png)

最后，我们找到了管理员的密码散列。

下一个任务是要找到 **user.txt.**

为此，我们必须研究文件上传或图像上传功能，以便远程进入系统。

![](img/bc364313ba68a151d2a3f1131cf75860.png)

我在网页上找到了上传功能，并通过上传反向外壳来利用这些功能。

![](img/5ad6cadff3841345ef4142ba15040d0e.png)

我开始用 Netcat 监听，上传那些反向 shell，把目标 shell 弄回来。

![](img/cbfeacd8bbd5b4294275c298a5d5828c.png)![](img/607a78883d0a2ef46f85e78d1487ce9a.png)![](img/ad90a87bbc25654ce54069c7100df768.png)

我导航到用户 mike 的位置，打开 user.txt。

这需要许可，所以我试着去调查**。bash_history**

![](img/2d1dfa5c0d03f4af8dfb74d3a58f4471.png)![](img/f236ec9a9738cfaa85f80c7a5021246f.png)

你可以在这里看到迈克的密码。

![](img/6305f49752053d1db7a7fd07154eaf1e.png)

进入 mike 帐户后，我们会启用 user.txt 文件。

![](img/d6ca2ff86918442d061c515e48445d1c.png)

Infosec Writeups 团队刚刚完成了我们的第一次虚拟网络安全会议和网络活动。我们有 16 位出色的演讲者，他们主持了非常有价值和鼓舞人心的会议。要查看演讲者和主题列表，请点击此处。

[](https://iwcon.live/) [## IWCon2022 — Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)