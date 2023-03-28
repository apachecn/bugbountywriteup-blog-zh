# HTB·塔比

> 原文：<https://infosecwriteups.com/htb-tabby-writeup-e5d25fb57329?source=collection_archive---------1----------------------->

## 目录遍历| LXD | RCE |弱密码

![](img/701ec7ed30beea9ff142fe447248bda4.png)

# 摘要

该网站利用了一个不充分的安全验证，即回溯系统的敏感文件。信息泄漏导致访问 host-manager 门户，暴露其版本易受远程代码执行的攻击。

发现该用户是某个组的成员，如果该组被利用，就会让该用户成为超级用户！

**使用的工具:** `nmap` | `apt-file` | `fcrackzip` |囊袋| `msfconsole`

# 侦察和计数

**Nmap 扫描**

![](img/b28893a0149a4e0acadf8aa393c1fc85.png)

运行 nmap 以查找开放的端口

![](img/0923507ea52559483127fa7a8fe5dbaf.png)

该网站的源代码揭示了一个可能被利用的`file` 参数:

`http://megahosting.htb/news.php?files=statment`

![](img/f4ae6390c7be0135e5eab9badbe65c3d.png)

在本地机器的 **/etc/hosts** 文件中添加 IP 地址主机名。

![](img/322eb60fa70a4d5cead51830618e330d.png)

在 burpsuite 中，我尝试了如下所示的点点斜线攻击:

![](img/c4b02aa9ec7c92a1344355cd099c04eb.png)

文件参数确实容易受到**点点斜线攻击**。它不限制用户访问上层文件系统目录。

![](img/608c4189f29304ad662ad1a1114124f4.png)

## 端口 8080 上的 Tomcat9

在[http://10 . 10 . 10 . 194:8080](http://10.10.10.194:8080)上，在页面底部提到了以下内容。

![](img/e61ba03d67ce690414ca311957933b9e.png)

## Tomcat9 用户文件位置搜索

*我试图通过我们发现的 LFI 漏洞来搜索上述 xml 文件。但是没有快乐。*

所以现在为了理解 Tomcat 文件系统结构以找到潜在的用户名/密码，我使用了工具`apt-file` **。**

我安装了`apt-file`命令来理解 tomcat 文件的结构和敏感文件的位置。这样，我就可以在端口 80 上发现 LFI 漏洞中注入相同的文件位置。

> $ sudo apt-get 安装 apt 文件
> 
> $ sudo apt-文件更新
> 
> $ sudo apt-文件搜索 tomcat | grep 用户

![](img/ca9ed728ccfc0f55953c28ba93661658.png)

在浏览器上，您会注意到 **tomcat-users.xml** 文件中的内容是空白的。

![](img/3d50520a25b550dfdc5e64ead578c1d3.png)

但是，如果您使用 burpsuite 并访问“tomcat-users.xml”文件的相同位置，您将能够看到响应头中的内容。

![](img/8399f70ba4871b613c6e31ee4cd0df8f.png)

下面是回复标题，您可以在其中找到用户名和密码，如下所示。

![](img/25a1de3a4ebf1026ebf9b968b91f804c.png)

## 端口 8080 上的主机管理器

以上凭据适用于以下门户:

> [http://megahosting.htb:8080/host-manager](http://megahosting.htb:8080/host-manager)

![](img/92379040300361dbed06086a67bd2c50.png)![](img/3978a6a23db6f1ad063346cb613d9a3d.png)

# 反向外壳

CVE 2020–1938 版本 9.0.31

[Tomcat 9.0.31](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938) 的默认配置易受攻击，从而导致远程代码执行(RCE)。关于这个漏洞的更多信息可以在[这里](http://tomcat.apache.org/security-9.html#Fixed_in_Apache_Tomcat_9.0.31)找到。

> 在运行任何漏洞利用之前，我会将机器重置为全新安装，以确保正确的漏洞利用正常工作。

使用`msfconsole`，我在`exploit/multi/http/tomcat_mgr_deploy`模块下创建了以下设置。

![](img/2030f124668ecb3afba4d6813b11c663.png)![](img/32875b3d1c7150d0a6e7b39e54494bb4.png)![](img/70224955a56c49dcde39dcd3b05e6bd2.png)

## 成功！！！

> 但是坚持住！

![](img/e64c571c46c05eb0bc8f52ea7f09e5d6.png)

无法从`tomcat`用户处检索到`user.txt`文件。

我们需要`ash`用户账号密码来获取`user.txt`文件。

# 横向运动

**横向权限提升**

![](img/4272fa6b5ad150a25bee8e1453900d16.png)

在`/var/www/html/files`中你会发现一个有趣的`16162020_back.zip`文件。

![](img/8a597b5f354ab276a67cffc2b8847c6b.png)

**我发现下载 zip 文件最简单的方法是这样的！**

在尝试解压这个文件时，我发现它有密码保护。

我运行命令`fcrackzip -u -c a -D -p /usr/share/wordlists/rockyou.txt '/home/komal/Documents/htb/tabby/16162020_backup.zip'`来破解密码。

![](img/fbe1e62580a72c14e4ad29601a99487a.png)

解压文件夹并查看文件内容后，我没有找到任何感兴趣的东西:-(

**直到……我这样做了……**

![](img/ba286d06c04ce21a5bde9e99ed89c1e0.png)

这是一个糟糕的密码策略

![](img/fd7ca40b904320084cb40c6c512df60e.png)

成功了！！！

> 第二阶段的任务一已经完成了！

# 垂直权限提升

用户`ash`群透露是 LXD 群成员。

![](img/d026eddb14ee307d667640af0d10bd1c.png)

LXD (Linux 守护进程)—Linux 容器管理程序

LXC (Linux 容器)

*   如果用户是`lxd`组的成员，那么这可能会导致获得根用户访问权限。更多关于 lxd 漏洞的信息可以在[这里](https://www.exploit-db.com/exploits/46978)找到。

> `'lxc'`是一个 linux 命令，通过它可以运行 LXD 的所有功能。

![](img/043a537c15bd55cb36ba5b95b37b3076.png)

通过运行如上所示的`lxc list`命令，我们可以看到如何开始运行我们的第一个 LXD 实例:

> `*lxd init*`
> 
> `*lxc launch ubuntu:18.04*` —运行这个命令对我不起作用，所以我采取了不同的方法。

**但首先:**

在我的攻击者机器上，我从 github 下载了 Alpine container builder 脚本’,并运行以下命令:

> git 克隆[https://github.com/saghul/lxd-alpine-builder.git](https://github.com/saghul/lxd-alpine-builder.git)T2【CD lxd-alpine-builder . git
> 。/build-阿尔卑斯山

![](img/67d4990ce28639a28fe0620baac90000.png)

成功执行后，您将看到“alpine-v 3.12-x86 _ 64–2020 07 01 _ 1615 . tar . gz”文件已创建。现在，把这个文件放在你的本地网络主机上，然后传输到 ash。

![](img/61a7c6ee9ba300a9efc88502cf9dceb4.png)

启动 apache2 web 服务器来托管 gz 文件。

![](img/57e2c5deae90ca52b45ebd8825682e8c.png)

gz 文件通过 wget 检索到 ash@tabby 机器中。

![](img/2a3cd2b50250a9b5ad294722c571e18d.png)

导入 lxc 图像，并将图像名称设置为“alpine”

![](img/5c2c9bad85cd645e2f540600fb7203bb.png)

在这里，我们创建了一个名为“newprofile”的新容器，并将其设为特权容器。

![](img/e14b6a2318cb20620602f51592c19074.png)

在这里，通过特权容器(newprofile)，我们将主机的文件系统(容器磁盘源=/)挂载到路径“mnt”目录(路径=/mnt)以获得根目录。

![](img/0b3a1221f4ccb5f043b17b3ce354c023.png)

启动容器后，我们现在可以在“lxc list”下看到其活动状态为“running”。

![](img/d38b006d181b1abb4bac155d44b2bb5b.png)

最后，特权容器被滥用来获取外壳

> 第二阶段的任务 II 已经完成了！

![](img/233b28800f2ad553b981742b0ed27802.png)

# 补救

*   实施适当的 ACL 控制来停止回溯文件和目录。
*   从标准用户帐户中删除 LXD 组。
*   避免出于不同目的使用相同的密码。
*   始终使用至少 10 个字符的唯一密码/密码短语。

# 参考资料:

*   [https://www . RS-online . com/design spark/an-intro-to-Linux-file-system-management](https://www.rs-online.com/designspark/an-intro-to-linux-file-system-management)
*   [https://github.com/saghul/lxd-alpine-builder.git](https://github.com/saghul/lxd-alpine-builder.git)