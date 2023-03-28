# 水母年！走过

> 原文：<https://infosecwriteups.com/year-of-the-jellyfish-tryhackme-walk-through-4faa99d29423?source=collection_archive---------0----------------------->

我要解决的另一个房间叫做“ [**水母之年** *”。*](https://tryhackme.com/room/yearofthejellyfish) 难度等级为**的[试炼](https://medium.com/u/dc49a0a3cb16?source=post_page-----4faa99d29423--------------------------------)可以获得。**

**![](img/d52db219d17e4814a47750da5149508c.png)**

**#水母之年**

**#我们要做的第一件事是将部署的机器的 IP 和主机名添加到/etc/hosts:**

**![](img/5145f7c928c6cd553d2fc0a1598ff26f.png)**

**/etc/hosts**

**让我们做一个端口扫描:**

**![](img/25f1f5b356d331724360ce22525c8c66.png)**

**nmap 结果**

**有几个港口开放了。**

**让我们从端口 443 上的一个网站开始。进入网站，我们首先看到一个警告。我们可以看到该站点使用了一个自签名证书。点击查看证书进行查看:**

**![](img/17159c49040419e5b9a192c20487ddd2.png)**

**证书**

**这表明该证书有三个额外的主题别名。**

**让我们将它们添加到 hosts 文件中:**

**![](img/1979f1fcfd3603203a2f87d1d2ce0855.png)**

**/etc/hosts**

**继续访问该网站，我们看到一个静态页面:**

**![](img/8a64ea699207e4579efcde3341cea585.png)**

**看 **dev.robyns-petshop.thm** 和**beta . robyns-pet shop . thm .**没什么值得注意的。现在让我们看看**monitorr . robyns-pet shop . thm**子域:**

**![](img/40e121df632173cce40a786d33adc629.png)**

**监控 r**

**根据 GitHub [链接](https://github.com/monitorr/Monitorr) : **Monitorr** 是一个网络前端，用于实时显示任何网络应用或服务的状态。**

**我注意到页面底部有一个版本号: **1.7.6m****

**让我们检查一下 searchsploit:**

**![](img/8c6e7d1096191598badfe5f9af245999.png)**

**searchsploit**

**让我们利用这一点。第一个是授权旁路。让我们来看看:**

**![](img/63b878589ac06caacd613b9a0611b892.png)**

**授权旁路利用代码**

**不幸的是，如果我们浏览"***/assets/config/_ installation/_ register . PHP***"我们看到的是不可访问的:**

**![](img/2a0bac1a5c205d3f04c40d13b17c9a91.png)**

**让我们看看另一个漏洞:**

**![](img/8a697e61fc5e534d0cfc88994aab470f.png)**

**远程代码执行利用代码**

**浏览至“***/assets/PHP/upload . PHP***”:**

**![](img/0610417e08398933db8ec8ebe189882a.png)**

**OMG！让我们试试这个漏洞:**

**![](img/8b73ff9455b36349e43ceb0b5312fce6.png)**

**这个 python 脚本不处理 SSL 连接。我们可以将 ***verify=False*** 添加到 post 并获得忽略证书检查的请求:**

**![](img/835ae73f81f0f8b8c0bc816e5d8b8285.png)**

**现在，再次利用:**

**![](img/2e56afc7cb3444df5de95560f693e899.png)**

**它说“应该上传一个 shell 脚本。”根据漏洞代码，该文件应该上传到以下路径:“***/assets/data/usrimg****”*。哦，没有上传任何内容:**

**![](img/150b9244e7471b9105933308956daa6e.png)**

**让我们打印响应代码，看看发生了什么:**

**![](img/a9d245565d96391b9313b70dbb35b68e.png)**

**有东西跟踪我们并阻止这种利用运行:**

**![](img/8055666beb18df5c6f12d63dfbc893ce.png)**

**检查浏览器中的 cookies。有一种饼干叫“**is human”:“1”**。**

**![](img/73e1605466a627c11e183b7d6aece365.png)**

**将此 cookie 添加到发布和获取请求:**

**![](img/d4afab4f9f9b463907ece622e9fb1a07.png)**

**并再次利用:**

**![](img/83b6e59877ddb3cefe909ecd43c67b96.png)**

**我们可以看到一个错误:“ *she_ll.php 不是图像”*！**

**让我们调试漏洞代码并使用旁路文件上传方法(双扩展名):**

**![](img/e3384e6428e719ce113a3fec6e91204b.png)**

**它仍然不起作用:**

**![](img/f655b8de172756e1da9c1aa803530da2.png)**

**让我们看看安全检查是否区分大小写:**

**![](img/2bd3356b18b8e666d36d0094b2ce2f7c.png)**

**奏效了。它只是在寻找小写 php，而不是大写！**

**![](img/9fc129bd4799bb6bd064a0509d1d9c05.png)**

**切换到我的监听器，最后我们连接了一个 shell:**

**![](img/0a576e2124170ed7fae45e5079dbfb52.png)**

**让我们升级到一个更好的全交互 shell**

**![](img/d3523273915057deef6a37c4bfbd1dfb.png)**

**交互式外壳**

**让我们找到第一面旗帜:**

**![](img/fb1f4e045f1de212d4a47cf2962c5caa.png)**

**标志 1**

# **权限提升**

**我用 **linpeas** 来枚举。过了一会儿，在 SGID 区，我发现了一个定制的 SUID 文件**快照**，因为除了这个，所有的项目都是绿色的。**

**![](img/69e29080ee7ca498d45d3c75693a5c48.png)**

**SGID**

**检查机器上安装的版本:**

**![](img/345d5ad7d89f63f67fbe3a3b4014a987.png)**

**快照版本**

**让我们在 searchsploit 中查找 snapd 可用的漏洞:**

**![](img/4a37d5de7e1685e6ff40a1c9c7592863.png)**

**让我们通过使用 **wget 直接从 GitHub 下载并执行来尝试这两种方法。第二个漏洞奏效了。****

**![](img/76d392b30eddc4cd7703f25f5626b916.png)**

**下载 dirty_sockv2**

**我们现在是**根**，我们可以从 **/root/root.txt.** 中获取根标志**

**![](img/a259a784430a715f957f965d43f4d31f.png)**

**根标志**