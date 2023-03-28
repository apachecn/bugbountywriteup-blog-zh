# 赏金黑客 Tryhackme

> 原文：<https://infosecwriteups.com/bounty-hacker-tryhackme-97a02bb16e93?source=collection_archive---------1----------------------->

## CTF 报道

欢迎回来，了不起的黑客们我在 TryHackme 上又想到了一篇 CTF 的文章。

![](img/30eadd861a569741b73d9cdf7ceaa58f.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[静止不动](https://unsplash.com/@stillnes_in_motion?utm_source=medium&utm_medium=referral)

首先，我开始扫描目标，看是否有任何感兴趣的服务和端口被暴露以进入系统。

我发现三个端口打开了。

![](img/3a9daa39823b7de13a99a6a6b9a86e76.png)

22，21，80 被打开

于是我开始用“FTP target-IP”进入 FTP。

![](img/c0fba4fc1f4871dd873e83b445d936bd.png)

进入 FTP 后，我发现两个文本文件是“locks.txt”和“task.txt”。

我查看了 **locks.txt** 和 **task.txt.** 中的内容

![](img/1af568568628db9f952d768559766f32.png)![](img/0b730721c74d6a3d2e077549dcf9e27a.png)![](img/fb1c0d0442679abb6d5a5b6d33f46b00.png)

从这两个文本文件中，我找到了受 ssh 保护的用户“lin ”,因此我决定使用“locks.txt”进行暴力破解。我找到了“lin”用户的密码。

![](img/bea1e297a757a72fc8fef795b8c9e40b.png)

> 通过 **ssh 以**林**的身份登录后，我发现了 **user.txt** 。**对于 **root** 登录权限，我使用“sudo -l”

![](img/3f2d9446af147886ad9fcef873d06fe0.png)

> 以获得 root 登录的任何帮助或提示。

我输入焦油二进制 GTFObins.com 绕过 **sudo** 根登录。我找到了一个命令。

![](img/e286a6b788f70c34979d276560a545eb.png)![](img/79e839685ef744feb146818f3b900435.png)

我希望你有一些知识来完成这篇文章。以后请关注我更多的文章。