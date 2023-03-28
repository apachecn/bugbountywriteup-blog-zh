# 杀死须藤，得到根

> 原文：<https://infosecwriteups.com/killing-sudo-getting-root-5c476381fddd?source=collection_archive---------0----------------------->

## 使用 Sudo 黑仔获得 Root 访问权限

![](img/0d2b21a92c9e6d5a2aa102ad84c874f3.png)

如果你是黑客、开发者或者系统管理员，你很可能用过 Sudo。

Sudo 代表“超级用户 do ”,是 Unix 类系统中的一个程序。如果用户提供 root 密码，它允许用户以超级用户的安全权限执行程序。(如果用户拥有超级用户权限，则为该用户的密码！)

由于 Sudo 的强大功能，程序中的任何弱点或错误配置都可能是灾难性的。恶意用户可以将其权限提升到 root 用户，从而完全控制服务器。今天，我们来谈谈一个旨在利用 Sudo 程序弱点的工具。

# 介绍 Sudo 黑仔

Sudo 黑仔是一个工具，它可以识别和利用 Sudo 程序中的错误配置和漏洞，帮助您将权限提升到 Root 用户。

Sudo killer 将首先在系统上执行检查，检查错误配置、危险的二进制文件、与 CVE 相关的易受攻击的 Sudo 版本、危险的环境变量、带有脚本的可写目录、可能被替换的二进制文件等等。然后，它将为您提供一个命令或漏洞列表，您可以使用这些命令或漏洞来生成根 shell。

Sudo 黑仔 shell 脚本可以在此下载:

[](https://github.com/TH3xACE/SUDO_KILLER) [## th3 xace/SUDO _ 杀手

### sudo 剥削#滥用 Sudo #利用 Sudo #Linux 权限提升 Linux 权限提升通过 SUDO…

github.com](https://github.com/TH3xACE/SUDO_KILLER) 

# 使用须藤黑仔

将须藤黑仔脚本上传或下载到目标机器后，不需要额外的设置。是时候开始检测一些漏洞了！

## 扫描服务器

您可以通过运行以下命令来扫描机器:

```
./sudo_killer.sh -c -e -r report.txt -p /tmp
```

看起来很困惑？别担心，下面是选项含义的分类:

```
-c : include CVE checks for that sudo version-i : import from extract.sh (used for offline mode)-e : export /etc/sudoers-r : generated report name -p : path to save exports and generated report-s : supply user password for sudo checks -h : help
```

有时，您还应该通过运行以下命令来更新苏多黑仔的 CVE 数据库:

```
./cve_update.sh
```

请注意，运行 Sudo 黑仔脚本本身不会获得根 shell。它只是扫描系统中可能存在的弱点，并为您指出下一步。

## CVE 利用权限升级

那么，我们如何着手获得须藤黑仔的根壳呢？

一种方法是扫描机器上的特权提升 CVE 并利用 CVE。要使用 Sudo 黑仔扫描 CVE，您可以运行:

```
./sudo_killer.sh -c
```

这将输出一个报告，其中包含一些有价值的信息，包括机器上当前使用的 Sudo 版本，以及与该 Sudo 版本相关的 CVEs。

在报告中，须藤黑仔将链接到有关 CVE 的信息。它还会链接到存储在。/利用目录。运行该脚本将利用该漏洞，并为您提供根外壳。

## 特权提升的其他方法

还有其他的东西，须藤黑仔可以检查。Sudo 黑仔还将扫描易受攻击的环境变量、危险的二进制文件、可写脚本等等。扫描后，须藤黑仔将为其执行的每项检查生成一份报告。

即使黑仔的 Sudo 没有包含自动利用该漏洞的脚本，它通常也会在报告中提供说明，或者指向一个单独的文本文件，教您如何利用该漏洞。

对于一些苏多黑仔演示和例子，观看视频教程在这里:

[https://www.youtube.com/watch?v=Q8iO9mYrfv8&list = plqpkppauca 40 fmpmkwzlxqydle 7 RP l5 bml](https://www.youtube.com/watch?v=Q8iO9mYrfv8&list=PLQPKPAuCA40FMpMKWZLxQydLe7rPL5bml)

# 结论

Sudo 黑仔对 pentesters 和系统管理员都是一个有价值的工具。它帮助您识别 Sudo 程序中的错误配置和漏洞，并帮助您利用这些漏洞。

通过探索这些可能性来测试您自己的系统可以使您的系统更强地抵御可能的攻击。

感谢阅读！我错过了什么吗？请随时在 Twitter 上告诉我:

[](https://twitter.com/vickieli7) [## 李薇薇

### Vickie Li 的最新推文(@ vickieli7)。书呆子的专业调查员。黑客和安全。创造上帝…

twitter.com](https://twitter.com/vickieli7) 

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)