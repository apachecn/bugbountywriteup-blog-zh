# Tryhackme tsharkpcapanalysis 书面报告

> 原文：<https://infosecwriteups.com/tryhackme-tsharkpcapanalysis-writeup-7b9ed3a19ea3?source=collection_archive---------7----------------------->

[](https://tryhackme.com/jr/tsharkpcapanalysis) [## 网络安全培训

### TryHackMe 是一个免费的学习网络安全的在线平台，使用动手练习和实验室，通过您的…

tryhackme.com](https://tryhackme.com/jr/tsharkpcapanalysis) 

对于 Wireshark 分析:

[](https://kaleileriteknoloji.medium.com/tryhackme-pcap-analysis-room-official-writeup-b83418d70113) [## Tryhackme Pcap 分析室官方报道

### 问:21 号港口的旗帜是什么？

kaleileriteknoloji.medium.com](https://kaleileriteknoloji.medium.com/tryhackme-pcap-analysis-room-official-writeup-b83418d70113) 

**任务 1**

问:21 号港口的旗帜是什么？

答案:vsFTPd 2.3.4

对于这个问题，我们可以使用下面的 tshark 命令:

t shark-r pcap . PCA png-Y " TCP . port = = 21 "

![](img/4292ba189ae0cf7bf918e6d230efcef1.png)

在三次握手之后，服务返回横幅信息。

问:来自用户 X:)作为 NMAP FTP 服务用户名的 NSE 脚本的名称是什么？

答案:ftp-vsftpd-backdoor.nse

我们将使用以下命令:

find / -iname "*。nse" 2>&1|xargs grep "用户 X "

![](img/884733e404238aa7d9c0e4b0e3cc70ce.png)

问:在使用 VSFTPD 后门脚本扫描 NMAP 时运行什么命令？

答案:id

当 vsftpd 后门漏洞被利用时，会在端口 6200 上打开后门。

对于这个问题，我们可以使用下面的 tshark 命令:

t shark-r pcap . pcapng-Y " TCP . flags . push = = 1 和 tcp.flags.ack==1 和 TCP . port = = 6200 "-V |更多

![](img/6ed6f0d3c41d4576e967856e0820c75f.png)

问题:Metasploit 框架中包含相关 vsFTPd2.3.4 横幅信息的 FTP 服务的弱点是如何利用的？

答案:exploit/UNIX/FTP/vsftpd _ 234 _ back door

我们将使用以下命令:

find / -iname "*。rb" 2>&1|xargs grep "VSFTPD "

![](img/76b2d983eb07e490d876d6cf5c471240.png)

问题:

在 Metasploit 框架中通过 ftp 服务登录到系统后运行的第一个命令是什么？

答案:whoami

当 shell 打开时，id 命令指定 uid=0(root) gid=0(root)。使用 nohup，该进程继续在后台运行。用 echo 指定的表达式用来检查外壳是否打开。

![](img/4ca89feb950d6daecc9b8992d6ba15cd.png)

代号:【https://www.exploit-db.com/exploits/17491 

攻击者发出命令后，echo 命令会自动运行。

对于这个问题，我们可以使用下面的 tshark 命令:

t shark-r pcap . pcapng-Y " TCP . flags . push = = 1 和 tcp.flags.ack==1 和 TCP . port = = 6200 "-V |更多

![](img/0ee93b1d56fc51219d6e9cda1cb36251.png)

第一个命令:whoami

问题:kaleileriteknoloji 用户的用户 ID 是什么？

答案:1003

对于这个问题，我们可以使用下面的 tshark 命令:

tshark -r pcap.pcapng -Y "帧包含 kaleileriteknoloji" -V

![](img/ccdf0677b2818fb0e60a62d0f295379c.png)

kaleileriteknoloji 用户 id 设置为 1003。

**任务二**

问题:用 Ngrok 接收反向 shell 时使用了哪个端口？

Asnwer:4444

对于这个问题，我们可以使用下面的 tshark 命令:

t shark-r pcap 2 . pcapng-Y " TCP . flags . ack = = 1，tcp.flags.push==1，ip.src==127.0.0.1 "

![](img/1a659460bbf8e184beca220da650c8a2.png)

问题:在收到带有 Ngrok 的反向 shell 后，首先运行的是哪个命令？

答案:ip a

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 2 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 IP . src = = 127 . 0 . 0 . 1 "-V |更多

![](img/3dc2b60dcf090bf6bc6504ab9d2bbf29.png)

问题:在收到带有 Ngrok 的反向 shell 后，第二个运行的是哪个命令？

答案:whoami

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 2 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 IP . src = = 127 . 0 . 0 . 1 "-V |更多

![](img/56a9f71c751b4db1a335dcc6a91c4d72.png)

**任务三**

问题:哪个 web 漏洞被利用了？

答案:命令注入

应用命令后，我们可以控制请求和响应。

t shark-r PCA P3 . PCA png-Y " http and http . request . method = = POST "-V |更多

![](img/b026bd26b492cb2d3e1aacba1002afe6.png)

问题:在接收带有 bash 命令的反向 shell 时，哪个端口在监听？

答案:1234

应用命令后，我们可以控制端口。

t shark-r PCA P3 . PCA png-Y " TCP . flags . ack = = 1，tcp.flags.push==1 "

![](img/69a70baf0771211654e3f60c046d49d5.png)

问题:web 漏洞被利用的工具是什么？

答案:commix

应用命令后，我们可以控制用户代理。

t shark-r pcap 3 . pcapng-Y " http "-V |更多

![](img/86e4132a3df0026321f6732c2c84c2cb.png)

问:哪个命令用于运行 web 请求中的 base64 编码数据？

答案:eval

应用命令后，我们可以控制数据。

tshark -r pcap3.pcapng -Y "帧包含 base64" -V

![](img/82f907412f8004b1b144980cf8516494.png)

问:除了 bash 命令使用的反向 shell 之外，还使用了哪个有效负载？

答案:php/meterpreter/reverse_tcp

应用命令后，我们可以控制端口。

t shark-r PCA P3 . PCA png-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1" -V

之后我们必须读取有效载荷。

![](img/7b14bf2ad98a793e061c0277465b5587.png)

问:在检查有效负载内容时，使用了哪个函数来运行命令？

答案:shell_exec

我们将搜索 exec、system、shell_exec 命令。

tshark -r pcap3.pcapng -Y "帧包含 shell_exec" -V

![](img/28fc70909387896f7df2df6ca4570038.png)

问:在 bash 命令收到反向 shell 后，首先运行哪个命令？

答案:id

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 3 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 TCP . port = = 1234 "-V |更多

![](img/ac4f5902613ad3d2e014f7edadd96af6.png)

问:在使用 bash 命令获取反向 shell 后，使用哪个命令切换到交互式 shell？

答案:python -c“导入 ptypty.spawn('/bin/bash ')"

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 3 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 TCP . port = = 1234 "-V |更多

![](img/fc9a3fdf0a5cd6390661923d983e448b.png)

问:凭据所在文件的完整路径是什么？

答案:/home/siber/cred.txt

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 3 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 TCP . port = = 1234 "-V |更多

![](img/775bc07dcee24079862cfd5038843798.png)

问:凭据所在文件的密码是什么？

答案:12345

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 3 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 TCP . port = = 1234 "-V |更多

![](img/36cf819454714eebaf06abcac0b011df.png)

问:siber 用户使用了什么命令来提升权限？

答案:须藤素

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 3 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 TCP . port = = 1234 "-V |更多

![](img/9b4ad1aa3ad79eb64ff3764f7c0bd3e4.png)

问题:siber 用户密码的哈希是什么？

答案:$ 6 $ EELzOgDE $ yhab 47 TF 5 EC 7 Li/git 2 hmur 6 zrek 3 rat jfz 2 zh 3a 2 qcrhbmpo 4n . 0 buozuzavchyrfvs 2 ygybzoztbet 1 v1 FZ。

应用命令后，我们可以控制 TCP 流。

t shark-r pcap 3 . pcapng-Y " TCP . flags . ack = = 1 和 tcp.flags.push==1 和 TCP . port = = 1234 "-V |更多

![](img/0dce7b7ca84bc14244cdad04e82ca129.png)