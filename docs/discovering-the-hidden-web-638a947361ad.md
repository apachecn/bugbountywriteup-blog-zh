# 发现隐藏的网络

> 原文：<https://infosecwriteups.com/discovering-the-hidden-web-638a947361ad?source=collection_archive---------0----------------------->

## 如何使用 GoBuster 执行 Pentest 侦察

![](img/264f28179ed0557525905c7b24db9ff3.png)

劳伦提乌·约尔达切在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

开始攻击 web 应用程序的第一步是对目标进行侦察。Recon 是指黑客和渗透测试人员深入挖掘应用程序以收集信息并发现通常不向用户公开的附加内容的过程。

侦察的一个重要步骤是发现隐藏的内容，如模糊的子域，秘密目录和虚拟主机。今天，我们来谈谈一款帮助我们完成这些目标的侦查工具:GoBuster。

GoBuster 是一种暴力工具，用于发现目标 web 服务器上的子域、目录和文件(URIs)以及虚拟主机名。

# 安装 GoBuster

先从安装 GoBuster 开始吧！你可以在这里找到 GoBuster 的项目页面:

[](https://github.com/OJ/gobuster) [## OJ/gobuster

### 用 Go - OJ/gobuster 编写的目录/文件、DNS 和 VHost 破坏工具

github.com](https://github.com/OJ/gobuster) 

您很可能不需要从源代码构建项目。在大多数 Linux 发行版上，可以通过 apt-get 命令安装 GoBuster:

```
apt-get install gobuster
```

否则，如果您有现成的 Go 环境，您可以使用:

```
go get github.com/OJ/gobuster dns
```

# 使用 GoBuster

现在你已经安装了这个程序，让我们直接使用 GoBuster 来执行侦查吧！GoBuster 有三种可用模式:“dns”、“dir”和“vhost”。它们分别用于暴力破解子域、目录和文件以及虚拟主机。

## DNS 模式

DNS 模式用于 DNS 子域暴力破解。您可以使用它来查找给定域的子域。在这种模式下，您可以使用标志“-d”来指定您想要强制使用的域，使用“-w”来指定您想要使用的单词列表。

```
gobuster dns -d <target domain> -w <wordlist>
```

你可以使用你自己的自定义单词表，但是一个好的选择是使用在线发布的单词表。例如，Seclists Github 存储库中有一个相当广泛的用于子域强制的单词表:

[](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/namelist.txt) [## 丹尼尔·米斯勒/塞克里斯特

### SecLists 是安全测试人员的伴侣。它是安全期间使用的多种类型列表的集合…

github.com](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/namelist.txt) 

## 目录模式

Dir 模式用于查找特定域或子域上的附加内容。这包括隐藏的目录和文件。

在这种模式下，您可以使用标志“-u”来指定您想要强制使用的域或子域，并使用“-w”来指定您想要使用的单词列表。

```
gobuster dir -u <target url> -w <wordlist>
```

您可以在此处找到可使用的网页内容词汇表:

[](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content) [## 丹尼尔·米斯勒/塞克里斯特

### SecLists 是安全测试人员的伴侣。它是安全期间使用的多种类型列表的集合…

github.com](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content) 

## 虚拟主机模式

最后，您可以使用 Vhost 模式来查找目标服务器的虚拟主机。

虚拟主机是指组织在一台服务器或服务器集群上托管多个域名。这允许一台服务器与多个主机名共享其资源。在服务器上查找虚拟主机名可以揭示属于某个组织的其他 web 内容。

```
gobuster vhost -u <target url> -w <wordlist>
```

对于强力虚拟主机，你可以通过 DNS 模式使用与强力子域相同的单词表。

## 高级选项

以下是一些您可能会觉得有用的附加选项。GoBuster 有很多高级选项，可以用来指定它的行为。要查看每种模式的选项，您可以运行:

```
gobuster help <mode>
```

例如，在 dir 模式下，您可以使用-x 标志对具有特定文件扩展名的文件进行暴力破解:

```
gobuster dir -u <target url> -w <wordlist> -x .php
```

对于 dir 和 vhost 模式，可以使用-k 跳过 SSL 证书验证并抑制 SSL 错误:

```
gobuster dir -u <target url> -w <wordlist> -k
```

对于 dir 和 vhost 模式，您甚至可以使用-c 标志来指定应该伴随您的请求的 cookies:

```
gobuster dir -u <target url> -w <wordlist> -c 'session=123456'
```

# 黑客快乐！

作为一名黑客或渗透测试者，良好的侦查技能是成功的关键之一。和 GoBuster 是一个简单而强大的工具，添加到您的侦察工具包！祝你黑客生涯愉快，好运！

感谢阅读。请记住:在您无权测试的系统上尝试这种方法是非法的。如果您发现了漏洞，请负责任地向供应商披露。帮助我们的互联网变得更加安全。

我错过了什么吗？请随时在 Twitter 上告诉我。

[](https://twitter.com/vickieli7) [## 李薇薇

### Vickie Li 的最新推文(@ vickieli7)。书呆子的专业调查员。黑客和安全。创造上帝…

twitter.com](https://twitter.com/vickieli7) 

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)