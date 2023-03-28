# TryHackme LFI 报道

> 原文：<https://infosecwriteups.com/tryhackme-lfi-writeup-a3fff2fb5009?source=collection_archive---------1----------------------->

## 如何发现和开发 LFI

欢迎回到酷炫的黑客博客，我将向你展示一个有趣的话题本地文件包含 Tryhackme 演练。不浪费时间，让我们进入主题。

![](img/27eb8c8b1a049b54b1ef28a780613843.png)

部署目标机器后，我看到了目标网页。

![](img/ba4b962e6a533cc5231187ba4ade4c9e.png)

我得到了一个端点**？由此，我得出结论，这可能是一个易受攻击的参数。**

**？文件=../../../../etc/passwd** 用于查看密码文件，如果它对攻击者可见，会导致密码文件的敏感泄露。

我输入了 **passwd** ，它给出了结果，然后它容易受到本地文件包含的攻击。

![](img/47a7020836b03d81abe3cb62afc5703a.png)

我开始知道这里泄露了用户名列表。falcon 是名字泄露出去的用户之一。

为了找到用户的密码，我们需要寻找 **/etc/shadow** 文件。

![](img/a68b2a93811edcf6f6d6bdf608da26e5.png)

然后每个哈希格式的密码文件我们都要解密才能得到用户密码。对于猎鹰，我们哈希

![](img/c1499be516699e36daa1b5d79fa6b54a.png)

哈希格式以$6$……开头。)这种格式。在 hashcat 模式的帮助下，我们知道它在 shacrypt 中。

要解码这个 Shacrypt，我们使用以下命令:

> **hashcat-m 1800 hash . txt rock you . txt**

然后，您将获得这种散列类型的密码。

然后，是时候使用登录到 falcon id

终于找到密码的 ssh falcon@target_ip。

然后就可以看到 falcon 账号里的 user.txt 文件了。

![](img/7772e24e18e4bbdfb4e677f555e12e39.png)

下一个任务是找到 root.txt 文件，为此我们必须提升 root 权限。

我在终端中键入**“sudo-l”**，它给出了 **journalctl 作为提示。**

journalctl —可用于查询 systemd(1)的内容。

![](img/f734c090e2baad5c481b756f894421fc.png)

然后在 GTFO·宾斯的帮助下，我们可以升级为 root。

![](img/fbafc3b395afb7cbfed0bd9b096e3012.png)![](img/11a37384a43c45d13bef362760bc08e4.png)

这就是我们如何以 root 身份进入目标机器的方法。最后，我们拿到了根旗。

![](img/5916e99f38ea7f27ccc0973672e5a256.png)