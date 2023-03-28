# VulnHub-Momentum 2 演练

> 原文：<https://infosecwriteups.com/vulnhub-momentum-2-walkthrough-8addad2e6a8f?source=collection_archive---------0----------------------->

![](img/a151c31f9e3a480d51c52c60c583d76d.png)

VulnHub Momentum 2 是一个中等级别的 boot2root CTF 挑战，你必须非常彻底地执行一些代码审查，并利用一个无限制的文件上传漏洞来获得访问权限。

让我们从查找虚拟机的 IP 开始。为此，我使用了 Nmap。

![](img/82b44c2ee90018b5443a9b1a93d5133f.png)

寻找 IP

然后，让我们运行传统的 Nmap 扫描，以找到开放的端口。

![](img/e996605ec5ef7a87e0b517749b7286cd.png)

Nmap 扫描以查找打开的端口

我们可以看到两个开放的港口，

*   端口 22 — SSH
*   端口 80 — HTTP

由于 web 应用程序是最大的攻击载体，所以让我们访问网站。

![](img/53733d8c9993115802137c66b3b024cc.png)

网络应用

在这个主网页界面上，我们找不到任何有用的信息。因此，让我们执行一个目录蛮力，以找出隐藏的目录或文件。

Gobuster 可用于此目的。

![](img/049f3fadb52d4dfcf98ee7adb7512d4b.png)

启动 Gobuster

注意:记得使用 **html，php，bak，php.bak** 文件扩展名。否则你会在这一点后卡住。我不得不花很多时间来找出丢失了什么！！！

Gobuster 的结果如下:

![](img/c378d9739def9719dc82130249c61d36.png)

Gobuster 结果

让我们从 dashboard.html 的**开始。这是另一个具有文件上传功能的网页。**

![](img/36bf17a247de2fcfa220d201e1a52174.png)

dashboard.html

我试着上传了一个 php 文件。它不成功。只允许上传 **txt** 文件。

在 **js** 目录中，有一个 **main.js** 代码，告诉我们上传的文件发送一个 **POST** 请求到**ajax.php**文件路径。

![](img/a08404bb902d54851a5e8b57bbaecf01.png)

/js/main . JSP

目录 **/owls** 好像是上传文件的存放位置。

![](img/e7bf5f6bd409caf620fc2618423b1adc.png)

/猫头鹰

ajax.php.bak 文件对我们来说是最珍贵的文件。请仔细阅读。

![](img/22712d6f79073733a84f9a6fcfa0c6a2.png)

ajax.php.bak

*   admin 有一个 cookie 值。
*   一个更大的字母需要添加在 cookie 的结尾。
*   名为“**安全**的新 POST 参数应该与值“ **val1d** ”一起发送。
*   管理员可以上传 **php** 文件。
*   如果上传成功，则**返回 1。**

这仅仅意味着我们可以在操作 web 请求后，以管理员身份上传一个 php 反向 shell 到系统。

你可以使用 Kali/Parrot OS 中的**php-reverse-shell.php**文件。记得编辑主机 IP 和端口。

![](img/cb57f5e1e86dc3b42dfab1de82672510.png)

php-reverse-shell.php

点燃炸药。当拦截打开时，上传 php shell。

现在是操纵 web 请求的时候了。添加**管理 cookie** 值和新的 POST 参数**安全**，值为 **val1d** ，如下所示。

![](img/659f0712ea23b94370e3425b042772cf.png)

操作 web 请求

但是，admin cookie 值并不完整，因为它的末尾还需要一个大写字母。我们可以利用打嗝闯入者找到那封信。

向**入侵者**发送上述被操纵的请求。清除所有有效负载标记。然后在 cookie 值的末尾插入新的有效负载标记，如下所示。

![](img/0bb8b7b997df1442c22033cc59f5a07a.png)

插入新的有效负载标记

现在我们需要一个有效载荷。我们只需要大写的英文字母。

一个简单的 bash 脚本可以用来输出这些字母，如下所示。你可以从我的 [GitHub repo 获得 bash 脚本。](https://github.com/ravi5hanka/English-alphabet-generator-bash)

![](img/de39c6748579c34e72e3092cf2938c04.png)

bash 脚本生成英语字母表

执行脚本，复制字母并将其粘贴到 Burp 的有效载荷部分。

![](img/099c53e9e769246add71958cf4faab6a.png)

打嗝的有效载荷部分

然后开始攻击。攻击完成后，观察每个字母产生的响应。

你会发现除了 **R.** 之外，每个字母都以 **0** 回应

![](img/65a34c3ba7e3dd43e20632ed64c486db.png)

r 答复 1

所以， **R** 是 admin cookie 中缺失的字母。

随着 php 反向 shell 成功上传，我们不再需要打嗝了。

然后用 php shell 中使用的端口设置一个 netcat 监听器。

![](img/d5a6fc7901fd61a5480e38c79a14e045.png)

netcat 监听器

进入 **/owls** 目录点击上传的 php 反向 shell。您将立即得到反向 shell 的提示。

![](img/b2c46ac9d62ca56466ed09a036fdce1a.png)

反向外壳

现在，我们得到了系统的立足点。首先推荐你把哑壳升级成全交互的。您可以遵循这些[说明。](https://null-byte.wonderhowto.com/how-to/upgrade-dumb-shell-fully-interactive-shell-for-more-flexibility-0197224/)

经过一些枚举，我们可以在 **athena 的**主目录**中找到用户标志。**

![](img/62601accaf9541c020faef3b6dc8c8cb.png)

用户标志

还有一个文件叫做 **password-reminder.txt.** 我们可以从那个文件中获取用户 athena 的密码。

![](img/7e5b2576aa3020231b82e6b0f909334e.png)

雅典娜的密码

因此，我们可以通过提供密码将用户更改为 athena。记住在密码末尾使用星号( ***** )符号。

![](img/ca36897296a924953a014e7a878460e9.png)

将用户更改为 athena

我们拥有该系统的用户！现在我们需要向根努力。

当列出这个用户可以作为 root 运行的内容时，我们可以看到一个可以作为 root 运行的 python 程序。

![](img/c085fde6618a420c44f5173f9174ef4f.png)

须岛一号

它为给定的输入生成一个随机的 cookie 值。查看代码，我们会发现它执行 bash 命令来回应输出。因此，如果我们可以添加一些命令，它将为我们提供 root 访问权限。

![](img/6e3cc36db32f2726f4761846ae9869e7.png)

python 程序的源代码

首先，我尝试在种子值后面追加 **bash -i** 。举个例子，“**21；巴什-我”。**成功了，但是不稳定，最后成了个空壳。

因此，我们可以如下附加/bin/bash，

*   将 **/bin/bash** 复制到 **/tmp/bash**
*   然后将 **SUID 位**设置为新的 **/tmp/bash**

最后，您可以执行新创建的 **/tmp/bash** shell，瞧！！！我们是根！！！

![](img/31c217b6c4e48360dbcf46987ceb8a32.png)

生根

您可以在根目录下找到根标志。

![](img/68e838fddb1161fe7dcec98388512b3a.png)

根标志

我希望你喜欢这个挑战，也学到了一些新东西。你可以通过 [LinkedIn 联系我。](https://www.linkedin.com/in/ravishanka-silva-a632351a0/)

祝你在前方捕捉旗帜时好运！！！