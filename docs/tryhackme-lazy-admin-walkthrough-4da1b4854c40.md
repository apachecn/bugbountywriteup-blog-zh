# Tryhackme 惰性管理演练

> 原文：<https://infosecwriteups.com/tryhackme-lazy-admin-walkthrough-4da1b4854c40?source=collection_archive---------1----------------------->

![](img/548dd188ee17b5e11991ad72c89237b9.png)

玩家们好，在这个博客中我已经介绍了 tryhackme 中的[懒惰管理](https://tryhackme.com/room/lazyadmin)框，这是另一个初学者级别的机器，将涉及下面提到的主题

1.情报收集

2.扫描和计数

3.剥削

4.用户级访问

5.权限提升

6.根访问

如果你不喜欢看博客，那就看视频吧

像往常一样，将连接到 tryhackme VPN 并部署目标机器

## **信息收集**

![](img/12e909878edfed190e56a2b388d5b83a.png)

现在，我们使用以下命令开始基本的 Nmap 扫描

T4 Pn<machine ip=""></machine>

通过这次扫描，我们得到了一些结果

![](img/c7a6319ece226ae6cdc3b5dfc99c5ad8.png)

从上面的结果我们可以得出结论，有一个 http 服务正在机器上运行，让我们检查该服务，并开始枚举它。

![](img/e42845eae90bce79667ac91e4767bbf6.png)

对于枚举，我们将使用我们最喜欢的工具 gobuster 找到 web 服务器的隐藏目录。

![](img/6427419b75e5e2f330efb754e3081398.png)

从 gobuster 扫描中，我们发现有一个名为 content lets check 的目录

![](img/d417103fd45589689ad9a88e5ea9473f.png)

## 扫描和计数

我想它有一个 CMS 面板，名为 sweet rice，让我们进一步检查内容端点。为此，我们将再次使用 gobuster 工具来找出答案

![](img/30268eda3e8eb394bd3cc5a9b23d09bc.png)

在 gobuster 扫描之后，我们发现了一些有趣的文件夹，其中有一个非常有趣的文件夹叫做/inc，让我们来看看它有什么

![](img/817a05f14ae8b4ba60f32c43d8e103e9.png)

太好了，它有一个数据库相关的文件，让我们在这里检查 mysqlbackup 文件夹。它包含一个 MYSQL 备份文件，让我们下载它，看看它是否有任何有趣的信息

![](img/c5cb689693e9b5fec23f3cb2c97ca213.png)

同时，我们的 gobuster 扫描还发现了另一个名为/as 的端点，它有一个用于 sweet rice CMS 的登录面板

![](img/55175d08c8a07ae767992f9d2a38fa46.png)

好的，它要求凭据，默认凭据在这里不起作用，所以我将检查我们从 MYSQL 备份文件夹中获得的备份 SQL 文件，幸运的是，它有一个经理角色的凭据

![](img/da772ad665c72fa2a3ebef5e79bc48c4.png)

但是这里的密码是散列的，所以我们将使用[破解站](https://crackstation.net/)网站，通过使用这个我们找到了纯文本密码

![](img/c7bfba995aca0238bb212cb4da99637d.png)

使用此凭据，我们已成功登录 CMS 面板

![](img/566b810f48e72d7f0b78c4bc02bbd2ea.png)

现在是探索应用程序的时候了，在了解应用程序后，我创建了一个名为“ads”的功能，它具有将我们的 php 代码上传到网站的功能，所以这是我们感兴趣的地方。因此，现在我们将上传我们著名的反向 shell 代码，其中包含用于连接的机器 IP 和端口，并进行反向连接

## 剥削

![](img/2b880c4344c76ab42d9781f7f0bbeea7.png)

让我们启动 Netcat 监听器。

![](img/7b4802008af364a15bf31f49bf00fa23.png)

现在访问文件的端点，您将得到一个反向的 shell 连接

[https://machine IP/content/Inc/ads/](https://machineip/content/inc/ads/)

![](img/c9ad0680d17992a5400698b699706e8e.png)

现在点击文件你将成功地获得 Netcat 上的反向连接。

![](img/f5162d3853ac04dfbc2143b3d4bd6ee6.png)

现在是获取用户标志的时候了，但是我没有在这里包含这个标志。但是可以通过 cat 命令获得。

![](img/53f57fb9926e0a0600eb94452a91ab36.png)

## 权限提升

现在是获取根特权和根标志的时候了，所以我开始枚举 sudo 权限。通过检查，我发现 backup.pl 文件可以使用我们的用户的 sudo 权限运行

![](img/762b548e3442d03a5ee70e05e32b02d5.png)

所以我们需要检查文件内容是什么，并发现它调用了一个 copy.sh 文件

![](img/cba4f4332933498c31ae738723f0db63.png)

现在我们需要检查 copy.sh 文件到底有什么，打开文件后，我们可以看到机器创建者制作的反向外壳代码，所以我们要做的只是将现有 IP 更改为我们的 IP

![](img/3c30c2bf5c13a942b54b0c3e68828c2f.png)![](img/457a06a1cf7cd78c45ef2523159cd2f3.png)![](img/4f2d70361cfefed3d8f321c308d87f7b.png)

幸运的是，该文件对所有用户都有执行权限，所以让我们启动 Netcat 监听器来获得一个根 shell 连接

![](img/7c9362a6d0e7a6f2bead5faf5cba33c7.png)

现在，我们将使用 sudo 权限运行 backup.pl 文件

![](img/44c4307f1de34b820af00c9f506757a1.png)

通过使用 sudo 权限运行该文件，我们成功地从根用户那里获得了一个反向外壳。

![](img/032fce61631101165c237a096ba21bbf.png)

现在是时候设置一个根标志了，我不会在这里显示这个标志，您可以通过 cat 命令查看它

![](img/7d7acabad68e8ef27a689b0388f71aee.png)

这是非常简单的初学者友好的机器，我希望你能学到一些新概念…祝黑客快乐，再见…