# 如何在 CTF 开始|完全创业指南

> 原文：<https://infosecwriteups.com/how-to-get-started-in-ctf-complete-begineer-guide-15ab5a6856d?source=collection_archive---------0----------------------->

![](img/28bc6a298b2c2d3b6d19bc4e7a6cbcdc.png)

嘿，朋友们，在这个博客里，我将分享你们在 CTF 是如何开始的:捕捉旗帜(“Jhande Ukhaadne Hai”)。所以让我们开始吧。

在了解如何在 CTF 开始之前，让我们先了解什么是 CTF，我们在 CTF 做什么，什么是旗帜，CTF 帮助你提高你的黑客技术。

# CTF:夺旗

CTF:“夺旗”是一种信息安全竞赛，要求参赛者完成各种任务。它是一种特殊类型的网络安全竞赛，旨在挑战计算机参与者解决计算机安全问题或捕获和防御计算机系统。通常，这些比赛是以团队为基础的，吸引了各种各样的参与者，包括学生、爱好者和专业人士。一场 CTF 比赛可能需要几个小时，一整天，或者几天。

# 为什么是 CTF？

由于其跨学科的性质，计算机安全对教育来说是一个挑战。计算机安全的主题从计算机技术的理论方面到信息技术管理的应用方面。这就很难概括构成计算机安全专业人员的感觉。

# 如何开始进入 CTF | CTF 在昆虫奖金中的重要性

# 夺旗挑战的类型

## 危险风格:

Jeopardy 风格的 CTF 在一系列类别中有几项任务。例如，web、取证、加密、二进制或任何其他内容。团队可以为每个解决的任务获得一些分数。更复杂的任务通常会得到更多的分数。系列中的下一个任务只能在某个团队解决了前一个任务后打开。那么游戏时间就超过了显示 CTF 赢家的数字总和

## 攻击防御风格:

攻防是另一种有趣的比赛。这里每个团队都有自己的网络(或者只有一台主机)，服务很粗暴。你的团队有时间修补你的服务，通常会开发冒险。所以，然后组织者添加比赛参与者，战斗开始了！你要为防御点保护自己的服务，为攻击点黑对手。

## 混合风格

混合比赛的可能形式可能会有所不同。这可能有点像战争游戏，对基于任务的元素有特定的时间。

CTF 游戏经常涉及信息安全的许多其他方面:密码学、速记、二进制分析、反向排列、移动安全等等。

# **挑战类型&工具**

## 密码术:-

在 CTFs 的情况下，目标通常是破解或克隆加密对象或算法以达到标志。

*   [FeatherDuster](https://github.com/nccgroup/featherduster) —一个自动化、模块化的密码分析工具
*   [哈希扩展器](https://github.com/iagox86/hash_extender) —用于执行哈希长度扩展攻击的实用工具
*   [PkCrack](https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack.html) —破解 PkZip 加密的工具
*   [RSATool](https://github.com/ius/rsatool) —利用 p 和 q 的知识生成私钥
*   [XORTool](https://github.com/hellman/xortool) —多字节异或密码分析工具

## 密写

在 CTFs 环境中，隐写术通常涉及到寻找隐藏在隐写术中的提示或标记。最常见的情况是，媒体文件将作为一项没有进一步指示的任务给出，参与者必须能够发现媒体中编码的信息。

*   [Steghide](http://steghide.sourceforge.net/) —隐藏各种图像中的数据
*   [Stegsolve](http://www.caesum.com/handbook/Stegsolve.jar) —对图像应用各种隐写技术
*   [Zsteg](https://github.com/zed-0xff/zsteg/) — PNG/BMP 分析
*   [退出工具](https://linux.die.net/man/1/exiftool) —读写文件中的元信息
*   [Pngtools](http://www.stillhq.com/pngtools/) —用于与 png 相关的各种分析

# 网

CTF 竞赛中的网络挑战通常涉及 HTTP(或类似协议)的使用，以及通过互联网传输和显示信息的技术，如 PHP、CMS(例如 Django)、SQL、Javascript 等。

*   测试网站安全性的图形化工具。
*   [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en) —为 chrome 添加调试网络请求的插件
*   [浣熊](https://github.com/evyatarmeged/Raccoon) —用于侦察和漏洞扫描的高性能攻击性安全工具
*   [SQLMap](https://github.com/sqlmapproject/sqlmap) —自动 SQL 注入和数据库接管工具
*   [W3af](https://github.com/andresriancho/w3af) — Web 应用攻击与审计框架。

## **取证**

在 CTF 环境中，“取证”挑战可能包括文件格式分析、隐写术、内存转储分析或网络数据包捕获分析

*   [Audacity](http://sourceforge.net/projects/audacity/) —分析声音文件(mp3、m4a 等)
*   [Bkhive 和 Samdump2](http://sourceforge.net/projects/ophcrack/files/samdump2/) —转储系统和 SAM 文件
*   [CFF 探险家](http://www.ntcore.com/exsuite.php) —体育编辑
*   [凭证转储](https://github.com/moyix/creddump) —转储 windows 凭证
*   [最重要的](http://foremost.sourceforge.net/) —使用头文件提取特定类型的文件
*   [NetworkMiner](http://www.netresec.com/?page=NetworkMiner) —网络取证分析工具
*   [Shellbags](https://github.com/williballenthin/shellbags) —调查 NT_USER.dat 文件
*   [UsbForensics](http://www.forensicswiki.org/wiki/USB_History_Viewing) —包含许多 USB 取证工具
*   [易失性](https://github.com/volatilityfoundation/volatility) —调查内存转储

**逆向工程**

CTF 中的逆向工程通常是将编译好的(机器码、字节码)程序转换回更易于人类阅读的格式的过程。

*   ApkTool —安卓反编译器
*   Barf —二进制分析和逆向工程框架
*   [二元忍者](https://binary.ninja/) —二元分析框架
*   [BinWalk](https://github.com/devttys0/binwalk) —分析，逆向工程，提取固件镜像。
*   [回旋镖](https://github.com/nemerle/boomerang) —将 x86 二进制文件反编译成 C
*   [Frida](https://github.com/frida/) —动态代码注入
*   [GDB](https://www.gnu.org/software/gdb/)—GNU 项目调试器
*   [GEF](https://github.com/hugsy/gef) — GDB 插件
*   [IDA Pro](https://www.hex-rays.com/products/ida/) —最常用的倒车软件
*   [Jadx](https://github.com/skylot/jadx) —反编译 Android 文件

**杂项(Misc)**

CTFs 中的许多挑战将是完全随机和前所未有的，只需要简单的逻辑、知识和耐心就可以解决。没有万无一失的方法来准备这些问题，但是随着你完成更多的 CTF，你将能够识别并有希望获得更多关于如何解决它们的线索。

# ***练习***

## [+] CTF 日历

[](https://ctftime.org/) [## CTFtime.org/关于 CTF 的一切(夺旗)

### 2020 年 5 月 18 日晚 8:05 团队“二六酮”添加为“二六九”的别名。2020 年 5 月 18 日下午 2:09,“The _ WinRaRs”团队…

ctftime.org](https://ctftime.org/) 

## [+]学习 CTF 的文章

[](https://github.com/ctfs/) [## CTFs

### 解散 GitHub 是超过 5000 万开发者一起工作的家。加入他们，发展您自己的开发团队…

github.com](https://github.com/ctfs/) 

## [+]如何开始 CTF

 [## CTF 野外指南

### “知道是不够的；我们必须申请。愿意是不够的；我们必须这样做。”-约翰·沃尔夫冈·冯·歌德我们很高兴…

trailofbits.github.io](https://trailofbits.github.io/ctf/) 

[+]首发 CTF

[](https://picoctf.com) [## picoCTF - CMU 网络安全竞赛

### picoCTF 是一款免费的计算机安全游戏，面向初中和高中学生，由…

picoctf.com](https://picoctf.com) [](https://tryhackme.com/) [## 黑客培训

### TryHackMe 是一个学习和教授网络安全的在线平台，全部通过您的浏览器完成。

tryhackme.com](https://tryhackme.com/) [](https://ctf101.org/) [## CTF 101 大楼

### 捕捉旗帜，或 CTF，是一种计算机安全竞赛。竞争者团队(或只是个人)是…

ctf101.org](https://ctf101.org/) [](https://ringzer0ctf.com/) [## 家庭戒指 0 CTF

### RingZer0 团队的在线 CTF 为您提供了大量的挑战，旨在通过以下方式测试和提高您的黑客技能…

ringzer0ctf.com](https://ringzer0ctf.com/) 

## [+]硬 CTF

[](http://plaidctf.com) [## 格子 CTF 2020

### 编辑描述

plaidctf.com](http://plaidctf.com) [](https://ctf.hitcon.org) [## HITCON CTF 2019

### 资格:线上 Jeopardy 2019 年 10 月 12 日上午 10:00 ~ 10 月 14 日上午 10:00(GMT+8，48 小时)目前 2019 HITCON CTF…

ctf.hitcon.org](https://ctf.hitcon.org) [](https://www.vulnhub.com/) [## 设计脆弱~ VulnHub

### VulnHub 提供的材料允许任何人获得数字安全、计算机…

www.vulnhub.com](https://www.vulnhub.com/) [](https://ctf.csaw.io/) [## 首页| CSAW

### CSAW 是世界上最全面的学生主办的网络安全活动，包括 9 场黑客竞赛…

ctf.csaw.io](https://ctf.csaw.io/) [](http://dragonsector.pl/) [## 龙扇区

### 龙区是一支波兰安全夺旗队。它创建于 2013 年 2 月，目前有 17 个活跃的…

dragonsector.pl](http://dragonsector.pl/) 

## [+] PHP 挑战赛(真实世界 CTF)

[](https://hackmd.io/s/rJlfZva0m) [## HackMD -协作降价知识库

### 无需会话的一行 PHP 挑战赛。上传###联系我 wupco1996@gmail.com # # #致敬

hackmd.io](https://hackmd.io/s/rJlfZva0m) 

## [+]网络/ Linux 挑战

[](http://overthewire.org/wargames/) [## 战争游戏

### OverTheWire 社区提供的战争游戏可以帮助您学习和实践安全概念，其形式为…

overthewire.org](http://overthewire.org/wargames/) 

## [+]虚拟专用服务器

[](https://digitalocean.com) [## 数字海洋——开发者云

### 我们通过直观的控制面板、可预测的……使在云中启动和随着您的增长而扩展变得简单

digitalocean.com](https://digitalocean.com) 

## [+]破解盒子(CTF 测试风格)

[](http://hackthebox.eu) [## 侵入测试实验室

### Hack The Box 为您的安全团队提供了丰富的信息和经验。培训您的员工或寻找新的…

hackthebox.eu](http://hackthebox.eu) 

## [+] Web 应用程序 CTF

 [## 网络安全

### 来自网络安全人员的网络安全挑战。

网络安全](http://websec.fr)  [## CTF 黑客 101

### 黑客 101 CTF 是一款游戏，旨在让你在一个安全、有益的环境中学习黑客。黑客 101 是一个免费的…

ctf.hacker101.com](https://ctf.hacker101.com/) 

## [+]二进制剥削 CTF

[](https://pwnable.tw) [## Pwnable.tw

### “关于 Pwnable.tw”是一个供黑客测试和扩展二进制开发技能的战争游戏网站。如何尝试找到…

pwnable.tw](https://pwnable.tw) 

## [+]逆向工程 CTF

 [## 서비스 오류 안내

### 编辑描述

reversing.kr](https://reversing.kr) 

## [+]密码术

 [## Cryptopals 加密挑战

### 我们不能比 Maciej Ceglowski 更好地介绍这些，所以先看看那篇博文。我们已经建立了一个集合…

cryptopals.com](https://cryptopals.com) 

# Youtube 频道:-

[](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w) [## 直播溢出

### 只是一个想要成为黑客的人...-=[ ❤️支持我]=-帕特里翁每视频:https://www.patreon.com/join/liveoverflow YouTube…

www.youtube.com](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w) [](https://www.youtube.com/channel/UC0ZTPkdxlAKf-V33tqXwi3Q) [## 黑客联盟

### HackerSploit 是领先的免费开源信息安全和网络安全培训提供商。我们的目标是使…

www.youtube.com](https://www.youtube.com/channel/UC0ZTPkdxlAKf-V33tqXwi3Q) [](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) [## IppSec

### 视频搜索:https://ippsec.rocks

www.youtube.com](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) [](https://www.youtube.com/channel/UCVakgfsqxUDo2uTmv9MV_cA) [## HackHappy

### 道德黑客频道，我专注于为有抱负的道德黑客，程序员，计算机…

www.youtube.com](https://www.youtube.com/channel/UCVakgfsqxUDo2uTmv9MV_cA) 

# 资源:-

[](https://aadityapurani.com/) [## Aaditya Purani -道德黑客

### 这里有几篇关于 CSAW·CTF 的文章。我们以 dcua 团队的身份参与进来，这是一群很棒的人，他们尽最大努力为…

aadityapurani.com](https://aadityapurani.com/) [](https://github.com/aadityapurani/My-CTF-Solutions) [## 阿迪亚普拉尼/我的 CTF-解决方案

### 我在各种夺旗挑战中尝试/解决的挑战的代码转储。不打算非常…

github.com](https://github.com/aadityapurani/My-CTF-Solutions) [](https://github.com/JohnHammond/ctf-katana) [## 约翰·哈蒙德/CTF-武士刀

### John Hammond |年 2 月 1 日在撰写本文时，该存储库将仅包含一系列工具和…

github.com](https://github.com/JohnHammond/ctf-katana)  [## 简介| CTF 资源

### 这个存储库旨在成为关于 CTF 竞赛的信息、工具和参考的档案。CTFs…

ctfs.github.io](https://ctfs.github.io/resources/) [](https://ctftime.org/writeups) [## CTFtime.org/报道

### 捕捉旗帜，CTF 队，CTF 评级，CTF 档案馆，CTF 报道

ctftime.org](https://ctftime.org/writeups) [](https://github.com/zardus/ctf-tools) [## zard us/CTF-工具

### 这是一组安装脚本，用于创建各种安全研究工具的安装。当然，这不是一个…

github.com](https://github.com/zardus/ctf-tools) 

## 希望你在看完这篇文章后会开始玩 CTFs。

特别感谢我的特斯拉朋友 [Aaditya Purai](https://twitter.com/aaditya_purani?lang=en) 分享不同类型的挑战。

特别感谢[Raihan Patel](https://www.instagram.com/the_techcraft/?hl=en)sir&[Ramya Shah](https://www.instagram.com/ramyashah4/?igshid=1dxt11ov26u2p)sir(古吉拉特法医科学大学)帮我评论这篇博客。

感谢大家的阅读:)

快乐黑客；)

喜欢我的作品就支持我吧！给我买杯咖啡，在[推特上关注我](http://twitter.com/impratikdabhi)。

[](https://www.buymeacoffee.com/impratikdabhi) [## impratikdabhi

### 嘿👋我刚刚在这里创建了一个页面。你现在可以给我买杯咖啡了！

www.buymeacoffee.com](https://www.buymeacoffee.com/impratikdabhi) 

网址:-【https://www.pratikdabhi.com/ 

insta gram:-[https://www.instagram.com/i.m.pratikdabhi](https://www.instagram.com/i.m.pratikdabhi/?hl=en)

推特:-[https://twitter.com/impratikdabhi](https://twitter.com/impratikdabhi?lang=en)

YouTube:-[https://www.youtube.com/impratikdabhi](https://www.youtube.com/impratikdabhi)