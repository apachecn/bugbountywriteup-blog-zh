# Tryhackme Erlik 机器记录

> 原文：<https://infosecwriteups.com/erlik-machine-writeup-4565f27a5695?source=collection_archive---------2----------------------->

[](https://tryhackme.com/jr/erlikmachine) [## 网络安全培训

### TryHackMe 是一个免费的学习网络安全的在线平台，使用动手练习和实验室，通过您的…

tryhackme.com](https://tryhackme.com/jr/erlikmachine) 

[https://tryhackme.com/jr/erlikmachine](https://tryhackme.com/jr/erlikmachine)

首先，让我们做一个 ping 扫描。

![](img/94a5bd2a68f625738185f1fe730b469e.png)

我们自己找 IP 地址来确定我们受害机的 IP 地址。

受害者 IP 地址:10.10.10.129

版本扫描是通过 Nmap 完成的。

![](img/fcdbd2c2106e47d3a331a2942dfec567.png)

我们看到的是 8000 端口。我们看到有一个 soap 服务。

![](img/8a35131f04edb00a34a2ed839d4a7665.png)

我们正在查看 wsdl 链接。

![](img/f8584aa94b4115696f8544d038fd2539.png)

同时，我们可以通过 python 使用 suds 模块来查看我们可以使用的方法。

![](img/297aa4ebd63602daae4ec09ec20bbd7a.png)

我们为“sorgula”方法提供 sql 注入控制。

![](img/849f3bcbd3c754bbb00bd00753edfd03.png)

我们在列表中显示用户名和密码对。我们尝试用这个用户名和密码对登录系统。

![](img/68c7e1b3f59a4aa8e365408855ace8f9.png)

让我们来查看 user.txt 文件。

![](img/6d36ec2f2b0a43fc9cb447282417c3e2.png)

我们使用“sudo -l”来列出用户权限或检查特定的命令。在我们使用“须藤素”之后。比我们查看 root.txt

![](img/9d00e8abb95c6f362e49277c4435df20.png)