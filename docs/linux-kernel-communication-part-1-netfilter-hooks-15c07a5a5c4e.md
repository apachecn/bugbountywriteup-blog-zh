# Linux 内核通信— Netfilter 钩子

> 原文：<https://infosecwriteups.com/linux-kernel-communication-part-1-netfilter-hooks-15c07a5a5c4e?source=collection_archive---------0----------------------->

![](img/ede711a930d16503376938d73f72c25b.png)

我一直对事物如何工作很感兴趣，尤其是计算机如何工作。大约一年前，我发现自己在研究 Linux 内核，或者更准确地说，我发现自己在研究如何创建自己的可加载内核模块(LKM)，这样我就可以做任何事情(嗯，几乎任何事情)。

我想做的第一件事是构建一个 LKM，它可以从外部获取任何命令，解析它，然后执行它。起初，我想到使用一个套接字作为监听器，在单个端口上，使用单个协议，这样它将成为我的通信工具；但是后来我想到了别的事情——为什么不监控进入机器的每个数据包呢？这样我就不需要担心为更多的端口或协议创建更多的套接字。

# 内核

内核是每个操作系统的中心。它包含所有的定义和指令，让机器知道如何管理它的资源。

Linux 机器的内存(RAM)分为两个空间，内核空间和用户空间。在 Linux 机器中，CPU 有两种执行模式，内核模式和用户模式。用户模式是用户程序的非特权(即只能访问存储器的用户空间)模式，而内核模式是用于任何内核目的的特权模式。当处于内核模式时，CPU 认为内核知道自己在做什么，因此会执行每一条指令，而不会问任何问题。

更多阅读请参见参考文献[【1】](http://www.linfo.org/kernel.html)

# Netfilter 挂钩

什么是网络过滤器？

来自 netfilter 项目文档:

> netfilter 是一个用于数据包处理的框架，在普通的 Berkeley 套接字接口之外。它有四个部分。首先，每个协议都定义了“钩子”(IPv4 定义了 5 个钩子)，它们是数据包遍历协议栈时定义明确的点。在每一个点上，协议都将使用数据包和挂钩号调用 netfilter 框架。

换句话说，netfilter 是一个工具，它赋予你使用回调来解析、改变或使用数据包的能力。

Netfilter 提供了一种叫做 netfilter hooks 的东西，这是一种使用回调在内核中过滤数据包的方法。有 5 种不同的 netfilter 挂钩:

```
A Packet Traversing the Netfilter System: --->[1]--->[ROUTE]--->[3]--->[4]--->
                 |            ^
                 |            |
                 |         [ROUTE]
                 v            |
                [2]          [5]
                 |            ^
                 |            |
                 v            |
```

1.  NF _ IP _ PER _ routing—当一个包到达机器时调用这个钩子。
2.  NF_IP_LOCAL_IN —当一个包的目的地是机器本身时，这个钩子被调用。
3.  NF_IP_FORWARD —当数据包发往另一个接口时，会调用此挂钩。
4.  NF_IP_POST_ROUTING —当一个包在返回网络和机器外部的途中时调用。
5.  NF_IP_LOCAL_OUT —当一个数据包在本地创建并被发送出去时，这个钩子被调用。

# 在内核中使用 Netfilter 钩子

要在内核中使用 netfilter 钩子，您需要创建一个钩子函数，并使用接收 struct `nf_hooks_ops*`的`nf_register_hook`或接收`struct net*`和`struct nf_hooks_ops`的`nf_register_net_hook`来注册它。您需要根据内核版本选择函数。该`struct`包含以下字段:

```
struct **nf_hook_ops** {
	*/* User fills in from here down. */*
	nf_hookfn		*hook;
	struct net_device	*dev;
	void			*priv;
	u_int8_t		pf;
	unsigned int		hooknum;
	*/* Hooks are ordered in ascending priority. */*
	int			priority;
};
```

*   钩子——一个指向函数的指针，一旦钩子被触发，这个函数就会被调用。这个函数来自类型`nf_hookfn`，它在不同版本的内核中有不同的签名。我建议您根据您工作的内核版本来搜索正确的签名(参见参考文献[2])。确保这个函数返回`NF_DROP`(丢弃包)`NF_ACCEPT`(让包继续它的旅程)或`NF_QUEUE`(如果你想将包排队到用户空间处理)。
*   hook num——钩子标识符之一(例如`NF_IP_POST_ROUTING`)。
*   pf —协议族标识符(例如，IPv4 的`PF_INET`)。
*   优先级—挂钩的优先级(如果系统中注册了其他挂钩)。该优先级可以是 enum `nf_ip_hook_priorities`中定义的优先级之一，enum`nf_ip_hook_priorities`在`netfilter_ipv4.h`文件中定义(如`NF_IP_PRI_FIRST`、`NF_IP_PRI_RAW`)。
*   现在，您可以忽略`*dev`和`*priv`字段。
    只是为了好玩，我直接从 Linux 内核源代码文档中添加一段引文—
    “*struct net _ DEVICE—设备结构。*
    *其实，这整个结构是一个很大的错误。它将 I/O*
    *数据与严格意义上的“高级”数据混合在一起，它必须了解 INET 模块中使用的*
    *几乎每一种数据结构。”*

# 代码示例

在本例中，我将向您展示一个简单的 LKM，它丢弃任何 UDP 数据包(目的地为端口 53 — DNS 的 UDP 数据包除外)，并接受任何 TCP 数据包。任何其他数据包都将被丢弃。

## 代码概述

*   第 21 行—检查`skb` ( `sk_buf` —套接字缓冲区)是否为空，如果是，则让该数据包继续其路由。
*   第 24 行—提取 IP 协议报头，以便我们稍后可以使用它来获得有关数据包的更多详细信息。
*   第 25 行和第 31 行—使用我们之前提取的 IP 报头，并检查该数据包使用了哪种协议。
*   第 27 行—检查 UDP 报头的端口。
*   第 48 行和第 53 行—注册和注销挂钩。

## 生成文件

要编译一个 LKM，你可以使用一个叫做`Makefile`的东西，它是一个包含指令和设置的文件，这些指令和设置稍后会被`bash`中的`make`命令读取和执行。对于这段代码，我写了下面的`Makefile`:

```
obj-m := LKM.o
LKM-objs += simple_netfilter_LKM.oall:
 make -C /lib/modules/$(shell uname -r)/build M=$(shell pwd) modules
 rm -r -f *.mod.c .*.cmd *.symvers *.oclean:
 make -C /lib/modules/$(shell uname -r)/build M=$(shell pwd) clean
```

## 编译和测试

将 LKM 编译并插入`bash`:

```
# make
# insmod LKM.ko
```

和`rmmod LKM`将其移除。

我强烈推荐使用 Wireshark 或其他嗅探工具来查看模块如何影响你机器上的网络流量。

# 参考

[【1】](http://www.linfo.org/kernel.html)关于内核 Linux 信息工程
***bootlin***—内核源代码分版本
【Hello World】LKM 教程
[【4】](http://www.tldp.org/LDP/lkmpg/2.6/html/x181.html)编译一个基本的 LKM
Wireshark 官网

![](img/918a2b73782dd8dd1b6e6c9f9a8e3935.png)

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)