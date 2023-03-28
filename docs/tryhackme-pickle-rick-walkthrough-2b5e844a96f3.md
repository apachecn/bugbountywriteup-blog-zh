# Tryhackme 泡菜里克演练

> 原文：<https://infosecwriteups.com/tryhackme-pickle-rick-walkthrough-2b5e844a96f3?source=collection_archive---------0----------------------->

**穆基兰 B 写的**

本演练是关于 CTF 挑战，我们必须通过利用目标找到旗帜。因此，让我们投入到 Tryhackme 挑战中。启动机器后，我看到了一个网页

![](img/ae902c8cd35c0749b30acee86199116c.png)

然后，我进一步深入到一个网站，我得到了一个用户名，通过源页面

![](img/320744db9c310ac3c5a540419fc4f711.png)

然后搜索密码和登录页面，我使用 dirsearch 命令强行打开目录

![](img/e03a4f3330553ddbe9f378ab76feae81.png)![](img/bdccfe0c7dd42533a927d46b85b51df4.png)

经过蛮力我找出了 robots.txt 和 login.php 等有用的信息。

我深入研究了 robots.txt，得到了密码 Wubbalubbadubdub

![](img/7a1115e0b650bf1b83c0b0fac007a18c.png)

我使用这些凭证登录到 login

![](img/b85fef19f20ccca9ba063f57053dd725.png)

登录后，我成功地找到了外壳，我给了一些命令，是否有任何有用的信息被隐藏。

![](img/ea2ae58a3c2f8df340889266f39744a8.png)![](img/144a2c8c0dacf776a1db3da2af0cf25e.png)

在我输入 ls 命令后，它显示了一些有用的信息

![](img/e1cf7c01f4bc0f2462071e2b4aaaaa17.png)

我在 shell 中使用 cat Sup3rS3cretPick13Ingred.txt，但它被列入了白名单

因此，我从系统中反向连接，无论它是否会连接，为此我使用 pentest monkey 网站上的命令列表。首先，我使用 Perl 命令不起作用，然后我继续使用 python 命令，它起作用了！！！对我来说不错

我把 python 换成 python3 才管用！！！

![](img/1ab0b2ad1c59741e351c2c06e2cafe1a.png)![](img/2fe20ad350add9dcece3f64a066c6e67.png)

获得之后，shell 使用“cat Sup3rS3cretPick13Ingred.txt”获得了第一个标志

那是米索克先生的头发

![](img/6501ab9950b07e74538872c51dd2edc3.png)

然后进一步挖掘，我得到了特权升级的第二个标志

![](img/2aa46fe715e9ac93b449ab9213f57bfe.png)

使用命令 python 3–c ' import pty；pty.spawn("/bin/bash ")'

通过简单的技巧，我使用“sudo bash”作为根用户登录

![](img/4e214d78cb47fa99d5995a33ac7232fc.png)

然后我拿到了第二面旗子

![](img/242f2383a82a2fcb296c7ab49f454651.png)

最后，我得到了第三面旗帜

![](img/d344afc3e053208032e23754cfac4bf4.png)

下次我会想出一个新的故事。

感谢神奇的黑客们！！！