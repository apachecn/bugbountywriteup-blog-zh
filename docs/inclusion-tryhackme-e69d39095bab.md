# 包容尝试

> 原文：<https://infosecwriteups.com/inclusion-tryhackme-e69d39095bab?source=collection_archive---------2----------------------->

![](img/814d02204a8d94811d179d43a6553db3.png)

嗨，了不起的黑客们，我今天带来了另一个有趣的话题，那就是本地文件包含。本地文件包含是 OWASP 十大漏洞之一。

LFI 漏洞允许攻击者读取(有时执行)受害机器上的文件。这可能非常危险，因为如果 web 服务器配置错误并试图以特权访问运行，黑客可能会获得敏感信息。

好了，现在让我们进入实践环节:

![](img/f2217dea51f054616b99aa762c7c69bc.png)

首先，我使用 Nmap 扫描目标，无论端口是打开的还是关闭的。我开始知道端口 22 ssh 是开放的。然后进一步挖掘，我在网站上找到了一些路径，它们是:

![](img/52e0e8762a93fe488d208b803204aa20.png)![](img/515766a9b4368ae10f977ace48ce86ff.png)

哪一个有点像目标 IP/文章？name=lfiattack，然后我把 name=lfiattack 改成了../../../etc/passwd/哪个看起来

![](img/973477cc7b20f507ec60b5a9bb89ffb9.png)

我找到了 ssh 登录的用户名和密码，于是我启动了我的终端，开始输入凭证 BOOM！！！

![](img/8ae478cb1a8a5cd866daaa13bc7dd2c2.png)

我用 ***打开 user.txt 猫 user . txt***我得到了我的第一面旗帜。

> **用户标志**

*回复:60989655118397345799*

然后我开始翻出根旗。然后我开始用 ***sudo -l***

根本不起作用。所以我使用 **socat** 监听器权限提升

![](img/53191eb9a7bd388f5fab37785c44bfae.png)

然后我连接这个监听器，我使用这个命令。

![](img/e6f21991d74591b2ae4a8aa0f18af697.png)

falconfeast 更改为 root 用户。

![](img/862a4342038bbc61786fda43aac78272.png)

然后我浏览了根位置，找到了我的根标志。

![](img/73a60686eedd42a182e30d650659872c.png)

> **根标志**

*回复:42964104845495153909*