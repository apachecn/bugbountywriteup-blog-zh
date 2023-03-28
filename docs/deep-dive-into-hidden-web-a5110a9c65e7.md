# 深入隐藏的网络

> 原文：<https://infosecwriteups.com/deep-dive-into-hidden-web-a5110a9c65e7?source=collection_archive---------1----------------------->

## 如何使用 Gobuster 执行 Pentest Recon？

开始攻击 web 应用程序的第一步是对目标进行侦察。Recon 代表对目标 web 应用程序的侦察。Recon 是指黑客和渗透测试人员深入挖掘应用程序以收集信息并发现通常不向用户公开的附加内容的过程。

一个重要的步骤是发现隐藏的内容，如观察子域，秘密目录和虚拟主机。今天，让我们来谈谈一个帮助我们实现这些目标的侦察工具:GoBuster。

GoBuster 是一种暴力工具，用于发现目标 web 应用程序上的子域、隐藏目录和文件(URIs)以及虚拟主机名。

# **安装 GoBuster**

先从安装 GoBuster 开始吧！可以在这里 *找到 GoBuster 的项目 [*。*](https://github.com/OJ/gobuster)*

您很可能不需要从源代码构建项目。在大多数 Linux 发行版上，您可以通过 apt-get 命令安装 GoBuster。

`apt-get install gobuster`

否则，如果您已经准备好了 Go 语言环境，那么您可以使用:

`go install -v github.com/OJ/gobuster dns`

# 使用 GoBuster

现在你已经安装了程序，让我们直接使用 GoBuster 来执行侦查吧！GoBuster 有三种可用模式:“dns”、“dir”和“vhost”。它们分别用于暴力破解子域、隐藏的目录和文件以及虚拟主机。

## DNS 模式

DNS 模式用于 DNS 子域暴力破解。您可以使用它来查找给定域的子域。在这种模式下，您可以使用标志“-d”来指定您想要使用暴力的域，使用“-w”来指定您想要使用的单词列表。

`gobuster dns -d <target domain> -w <wordlist>`

你可以使用你自己的自定义词汇表，但是一个好的选择是使用在线发布的词汇表。例如，Seclist Github 存储库中有一个相当广泛的用于子域强制的单词列表:[danielmiessler/sec lists](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/namelist.txt)。

## 目录模式

Dir 模式用于查找特定域或子域上的附加内容。这包括隐藏的目录和文件。

在这种模式下，您可以使用标志“-u”来指定您想要强力攻击的目标域，使用“-w”来指定您想要使用的单词列表。

`gobuster dir -u <target.com> -w <wordlist>`

你可以在这里找到一个网页内容列表 wordlist 使用: [SecList](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content) 。

## 虚拟主机模式

最后，您可以使用 Vhost 模式来查找目标服务器的虚拟主机。

虚拟主机是指一个组织在一台服务器或服务器集群上托管多个域名。这允许一台服务器与多个主机名共享其资源。在服务器上查找虚拟主机名可以揭示属于某个组织的其他 web 内容。

`gobuter vhost -y <target url> -w <wordlist>`

对于暴力强制虚拟主机，你可以在 DNS 模式下使用与暴力强制子域相同的单词表。

## 高级选项

例如，在 dir 模式下，您可以使用“-x”标志暴力破解具有特定文件扩展名的文件。

`gobuster dir -u <target-url> -w <wordlist> -x .php`

对于 dir 和 vhost 模式，可以使用-k 跳过 SSL 证书验证并抑制 SSL 错误。

`gobuster dir -u <target-url> -w <wordlist> -k`

对于 dir 和 vhost 模式，您甚至可以使用“-c”标志来指定应该符合您的请求的 cookies。

`gobuster dir -u <target-url> -w <wordlist> -c 'sssion=123456'`

结束！！

**快乐黑客**

作为一名黑客或渗透测试者，良好的侦查技能是成功的关键之一。GoBuster 是一个简单而强大的工具，可以添加到您的侦察工具包中！

直到那时；

醒|吃|砍|重复🔥

![](img/584c793a824961c9ac68ef83d65cc4fd.png)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！