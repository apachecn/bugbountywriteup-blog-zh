# TryHackme—Django 简介

> 原文：<https://infosecwriteups.com/tryhackme-introduction-to-django-e24893895c69?source=collection_archive---------0----------------------->

## 夺旗类游戏

你好，神奇的黑客们，在这个博客中，我们将看到一个基于 Django 框架的酷 CTF 挑战赛。不浪费任何时间让我们开始吧。

在这次挑战中，他们给了我一些证书。我想做一些 Nmap 扫描来检查哪些端口是打开的。

![](img/b3df25727852d46cdde198dc4b5e9d65.png)

当我知道端口 22 和 8000 被打开后，我做了一个 Nmap 扫描。

![](img/eb219819c8ac065b5b290397ac676052.png)

所以我用提供给我的凭证进行 ssh 登录。

![](img/7ccf625da0b567a39d9af09ba15ac75c.png)

我通过 ssh 成功登录。

然后我继续挖掘，找到了第一个标志，在这个问题中，他们显示了管理面板的标志。我创建了一个超级用户。

![](img/84e13635494805ecf24607717427fcb5.png)

然后我在浏览器中打开管理面板，在 settings.py 中包含目标 IP(允许的主机=[0.0.0.0，127.0.0.1，目标 IP])

![](img/1800176f9c737342b5300db59e70af21.png)

然后我点击用户，它显示第一个标志。

## THM{DjanGO_Adm1n}

然后导航到 StrangeFox，你可以找到第二面旗帜。

![](img/de852c0691ea5103ae1115e840507baf.png)

## THM{SSH_gUy_101}

然后你会在 home.html 发现第三面旗帜

![](img/375c0c7f3dad078ef6cf1572c0cb1bd3.png)

感谢你和我一起度过了阅读这篇文章的美好时光！！！