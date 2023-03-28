# Cyber 2022[第 20 天]固件的出现| binwalking ' around the tree-Simple Write up

> 原文：<https://infosecwriteups.com/advent-of-cyber-2022-day-20-firmware-binwalkin-around-the-christmas-tree-simple-write-up-345f9525d20c?source=collection_archive---------1----------------------->

## 任务 25-固件|在圣诞树周围漫步-赛博 2022 的来临[第 20 天]-答案撰写和演练

![](img/40f616007205d4c869c532795d7df479.png)

> ***什么是固件逆向工程？***
> 
> *各种嵌入式系统，如摄像头、路由器、智能手表等。预装了固件，该固件在硬件处理器上运行自己的指令集。*
> 
> *它使* ***硬件能够与设备*** *上运行的其他软件进行通信。固件为设计者/开发者提供了在根级别进行改变的低级控制。*

**固件反转步骤**

*   固件首先从供应商的网站获得，或者从设备中提取以执行分析。
*   首先分析获得/提取的固件(通常是二进制文件)以确定其类型(裸机或基于操作系统)。
*   验证固件是加密的还是打包的。加密固件的分析更具挑战性，因为它通常需要一个棘手的解决方案，例如逆转以前的非加密固件版本或执行硬件攻击，如[侧信道攻击(SCA)](https://en.wikipedia.org/wiki/Side-channel_attack) 来获取加密密钥。
*   一旦加密的固件被解密，不同的技术和工具被用于执行基于类型的逆向工程。

> [***BinWalk***](https://github.com/ReFirmLabs/binwalk)***:****一款固件提取工具，通过针对* `*zip, tar, exe, ELF,*` *等多种标准二进制文件格式搜索签名，提取任意二进制文件中的代码片段。*
> 
> *Binwalk 有一个二进制标头签名数据库，根据该数据库执行签名匹配。*
> 
> *使用该工具的共同目的是提取一个文件系统，如* `*Squashfs, yaffs2, Cramfs, ext*fs, jffs2,*` *等。，它嵌入在固件二进制文件中。文件系统拥有将在设备上运行的所有应用程序代码。*

**打开机器的分割视图**

# 任务 25【第 20 天】**固件**宾绕着圣诞树走

## 1.文件 firmware 2.2-encrypted . gpg 反转后的标志值是多少？

```
cd bin
binwalk -E -N firmwarev2.2-encrypted.gpg 
```

![](img/4b93e0b5dac3403fcfcf01a50149bfca.png)

```
cd ..
cd bin-unsigned/
extract-firmware.sh firmwarev1.0-unsigned
```

![](img/dd09aaa985215ffd2df8414851dfb943.png)

> *密码:Santa1010*

![](img/34f8db7a8d1a036f9a96e1063be45bdf.png)

```
grep -ir paraphrase
cat fmk/rootfs/gpg/secret.txt
```

![](img/b79654efb3d7d33b74685a907d28062b.png)![](img/a7c6c3d4a62b1259d1baa9bf755e313f.png)

> *释义:圣诞老人@2022*

```
gpg — import fmk/rootfs/gpg/private.key
```

![](img/5babb77042c2cf19d08cdcbcb5c33f99.png)

键入我们找到的**释义**

![](img/50119439ddca3db20dcfe9c83bc92373.png)

```
gpg --import fmk/rootfs/gpg/public.key 
gpg --list-secret-keys
```

![](img/df2144a6fc04bd04b4af2c81d66194e0.png)![](img/016b22cbb3b90415a6ff5da06d1a84b1.png)

一旦密钥被导入，McSkidy 使用 gpg 命令解密固件。再次通过输入命令 **cd 更改目录..**然后是 **cd 仓**

```
cd ..
cd bin
gpg firmwarev2.2-encrypted.gpg
cat ~/bin/fmk/rootfs/flag.txt 
```

![](img/afae1b5dbff2154fc39a6c7441292ff9.png)![](img/ea6c4244c713a4bd1c0fa8c5227289f4.png)

```
Ans: THM{WE_GOT_THE_FIRMWARE_CODE}
```

## 2.二进制固件 1.0 版 _unsigned 的释义值是多少？

```
Ans: Santa@2022
```

## 3.逆向加密固件后，能找到 **rootfs** 的内部版本号吗？

使用下面的命令查找 rootfs 的固件

> ***确认一下，你在 rootfs 目录下***

```
ls -lah * | grep rootfs
```

![](img/4df2a54a29009eb98c4809f25bd4ee89.png)

```
Ans: 2.6.31
```

感谢您的阅读！！

黑客快乐~

```
Author : Karthikeyan Nagaraj ~ Cyberw1ng
```

THM，TryHackMe，TryHackMe 2022 年网络时代的到来，TryHackMe 2022 年网络时代的到来第 20 天，道德黑客，写，走过，TryHackMe 2022 年网络时代的到来第 20 天答案

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！