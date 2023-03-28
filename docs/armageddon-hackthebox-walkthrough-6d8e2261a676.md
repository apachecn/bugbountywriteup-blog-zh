# 世界末日:黑客盒子演练

> 原文：<https://infosecwriteups.com/armageddon-hackthebox-walkthrough-6d8e2261a676?source=collection_archive---------0----------------------->

## 描述

回去后很长一段时间用另一台 [**HackTheBox 机**](https://app.hackthebox.eu/machines/Armageddon) 预排。希望你喜欢。只需在你的/etc/hosts 文件中添加 armageddon.htb，然后开始你的 pawing 过程。

![](img/c38920932aa1119927374c2ddbfcd52c.png)

> 获得的知识

1.  搜索漏洞
2.  初始 shell 的 Metasploit
3.  SQL 命令
4.  dirty_sockv2 漏洞利用
5.  权限提升

> 端口扫描

在我的端口扫描过程中，我首先使用 [**rustscan**](https://tryhackme.com/room/rustscan) 来快速找出开放端口的数量…

![](img/b838e8d4ff9879795c715a6edda58ac8.png)

…然后在这些打开的端口上开始详细的 **Nmap** 扫描。这为我节省了扫描时间。

![](img/e2dffce1441e50a1466bb583d8f36bf8.png)

> 网络侦察

所以我们先列举一下 80 端口。我决定启动一个 **ffuf** 扫描，得到了 **robots.txt** 和一些不允许进入的文件& &目录。

![](img/0811cb76beca6ebf6449af4d74a2aa28.png)

现在，我访问了网页，并决定使用 **WhatWeb** 命令来识别该网站运行的服务，这是结果，它是 Drupal。

![](img/1557e6889e05a2e3bafe71e8ad4fc929.png)

现在是时候搜索 drupal 7 可用的漏洞了，为此我们可以使用一些方法，如在 Linux 终端中使用 searchexploit 或 Google CMS 名称。

![](img/c96f65d6f5e4f72976457c9dc2a0243d.png)

Searchexploit 确认了 drupal 7 中存在一个 Metasploit 模块，我们也可以看到 google 的结果。

![](img/665718057c9886ddc81bbd90d91b4a73.png)

打开 msfconsole，搜索 **Drupal 7** 漏洞。

![](img/91ce95dff1fabdff56ff38cf517e5a92.png)

选择漏洞并设置所需的选项

![](img/0d7727e2adb7d6821e06a822c9e2f5e0.png)

并点击 exploit 来获取 meterpreter 会话。

![](img/a462beb2769d20fb38abe32e2b277087.png)

现在，我键入 **shell** 命令来获得一个交互式会话，并检查安装了哪个 python 版本，但由于这个错误，我无法获得交互式 shell。

![](img/1518d2035cf8be62fe71802f0365f729.png)

因此，继续我的枚举，我遇到了一些文件，给我 MySQL 登录凭证。

![](img/44a54124030ecec10f0967f4123175f6.png)

搜索所有这些文件，您将获得 MySQL 凭证。

![](img/b125d4c7a2f8a1394b8ca656c8356067.png)

现在，我使用这些凭证尝试获取 drupal 用户的详细信息。首先，让我们找出可用数据库的名称。

![](img/fc96ebe199da8db8ee428ca655dfa64c.png)

因此，我们得到了数据库名称，现在尝试找出 drupal 数据库中的表。

![](img/9083ce988af7a657a366286bcffb3fed.png)

因此，这里列出了表名。使用“users”表获得正确用户的密码散列。

![](img/03ac0fb77ccebf078c56f4a5508f719d.png)

复制 **hash** ，用最知名的工具 **JohnTheRipper** 和最佳密码列表 **rockyou.txt** 破解。

![](img/cef32fa10e2be8bc5762fdbc1b24731f.png)

现在使用凭证 SSH 到机器，我们得到了第一个标志。我首先检查的是当前用户的权限。所以，这个用户有一些权利。( [**LinPEASH.sh**](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) 也说明了这一点)

![](img/027ea2c87520f1bc53a5b98d1248dca3.png)

是时候谷歌一下了。通过/usr/bin/snap install 搜索 google 上的"**特权提升，我得到了一些有用的结果。**

1.  [**Ubuntu Linux 中的权限提升**](https://shenaniganslabs.io/2019/02/13/Dirty-Sock.html)**(**[**dirty _ sock v2**](https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py)**exploit)**
2.  [**玩脏袜子**](https://0xdf.gitlab.io/2019/02/13/playing-with-dirty-sock.html)

这里发生的基本情况是，利用 snap API 来安装 snap。snap 实际上不做任何事情，但是包含一个 bash 脚本，它将添加一个用户作为安装挂钩。然后，它再次使用 api 删除快照，但用户仍然存在。因此，当我们运行该脚本时，它将在 **/etc/sudoers** 文件中添加一个拥有所有权限的用户，我们可以轻松地将我们的权限提升到 root。

![](img/ee24facc4864098d204fda74e429da8f.png)

在上图中，当我们解码 base64 编码的文本时，我们可以看到用户名为**“dirty _ sock”**的用户将被创建，其密码为**“dirty _ sock”**，该用户拥有所有权限。

所以，我们不需要从这个[**链接**](https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py) 的整个代码。只复制“TROJAN_SNAP”部分，用 python 解码 base64，保存在一个**【文件名】中。snap** 如果在受害者机器中解码时出现任何错误，则将其解码并保存在您自己的机器中，并使用**"**[**updog**](https://github.com/sc0tfree/updog)**或使用 python"** 在本地启动服务器进行传输。我会选择 updog 工具，它简直太棒了。在保存。快照文件。

![](img/e7f3adbb60c4705728ba5c56109a1f54.png)

现在在 **10.10.10.233** 中**收口**被安装。因此，在/tmp 目录中像这样传输文件并更改权限。

![](img/3b8b570355af8a6c92e4a6ffc5f2ef37.png)

使用 sudo 权限运行该文件，用户条目将在/etc/passwd 文件中列出。

![](img/678b233a9832afd8aa9d1d9cbb4e659f.png)

如果没有出现这种情况，请停止机器。现在，要将权限提升到 root 用户，请输入 **"sudo -i"** ，我们现在就要停机了。

![](img/19a5140712fad7070d1ae824ed8c9799.png)

去抓取你的 root.txt 文件。

为 OSCP 考试捐款👏

[![](img/715dd4278e844e40ee214a684fa56803.png)](https://www.buymeacoffee.com/MrLazy)

# 在你走之前

这是我的另一个黑客机器演练:-

[](https://shubham-singh.medium.com/doctor-htb-walkthrough-70bcb9eedefd) [## 医生:HTB 演练

### 描述

shubham-singh.medium.com](https://shubham-singh.medium.com/doctor-htb-walkthrough-70bcb9eedefd) [](https://shubham-singh.medium.com/academy-hackthebox-walkthrough-9102c5d79dee) [## 学院:黑客盒子演练

### 描述

shubham-singh.medium.com](https://shubham-singh.medium.com/academy-hackthebox-walkthrough-9102c5d79dee) [](https://github.com/Mr-Lazzy) [## 懒人先生-概述

### 网络安全爱好者🐱‍💻。拉兹先生有 7 个存储库可用。在 GitHub 上关注他们的代码。

github.com](https://github.com/Mr-Lazzy) 

鼓掌👏如果你喜欢的话。