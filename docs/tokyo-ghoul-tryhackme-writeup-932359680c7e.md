# 东京食尸鬼 Tryhackme 报道

> 原文：<https://infosecwriteups.com/tokyo-ghoul-tryhackme-writeup-932359680c7e?source=collection_archive---------5----------------------->

**作者 Shamsher khan 这是 Tryhackme room“东京食尸鬼”的特写**

[![](img/eeabc20f60c7fa30b6ba519f63be94a8.png)](https://www.tryhackme.com/room/tokyoghoul666)

https://www.tryhackme.com/room/tokyoghoul666

**房间链接:**[https://www.tryhackme.com/room/tokyoghoul666](https://www.tryhackme.com/room/tokyoghoul666)
**注:此房免费**

# 列举

![](img/1dffb74418da9c44d3b523bf94572f8a.png)![](img/0467c73a40897709cec7a37985348a29.png)

所以我们有 3 个端口

允许 FTP-21 匿名登录

SSH-22

HTTP-80

**问 1:** 开放了多少个端口？

> ***答案:3***

**问 2:** 用的是什么 OS？

> ***答案:ubuntu***

你找到其他食尸鬼给你的纸条了吗？你在哪里找到的？

![](img/32594b9bc8107c5a356b86c773fce769.png)

> ***答案:jasonroom.html***

登录到 FTP 服务器

![](img/89109fa440eb5aeb1f0630b348ddfab0.png)

下载这些文件

![](img/e7153658be8efd9ba4a9aaa979d0a854.png)

让我们检查一下这些文件

![](img/09bee3508a388a022addf250c098bdb8.png)

该文件等待一个解释，让我们尝试任何字符串作为密码，以检查它将返回什么输出。所以其说用“**rabina 2-z”**来拉它

![](img/cbdf931d1c62331d65cb0b3df11af4c2.png)

嘣它显示我的文件密码。让我们使用密码

![](img/0f3e7984ce042e68e2c44bd3cbc7f6eb.png)

程序给了我们另一个字符串，我想这是图片的密码，所以我用了 steghide

![](img/eb9d467d9c71962706ca0fa6ec456926.png)

这是摩斯密码，我们来破译

![](img/acfa56e50013d9de7fbfe795ee0ae754.png)

莫尔斯码给了我们十六进制字符串，让我们来破译它

![](img/b6018c9d651bb15350eb69e4558b9cd1.png)

[http://string-functions.com/hex-string.aspx](http://string-functions.com/hex-string.aspx)

十六进制字符串给我们 base64 字符串让我们解码它

![](img/116b408aa3159b735356362ac8313304.png)

我们发现隐藏目录让我们访问隐藏目录

```
[http://10.10.59.18/**********/](http://10.10.59.18/d1r3c70ry_center/)
```

![](img/a00dcbda0eb97eb8440c51fed590d9b4.png)

## dirb

![](img/a37dd7a8fdfab3836c07d1746d7c9bda.png)

我们找到了另一个子目录

![](img/85a3fece49eb161a1d7e78d455311805.png)

点击**是后**我看到这个参数是谁在服务器上调用的文件

index.php？view=flower.gif

也许我们可以将它改为返回并获取/etc/passwd

![](img/6a2b0929189b46b1688f55b1f772a429.png)

但是我们现在知道这里有一个漏洞，所以我们需要使用 html url 编码来绕过它

```
?view=%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd
```

![](img/60664a0355b89b35116fb83d4e98cd2c.png)

Boom 我们得到了/etc/passwd，带有一个用户名: **kamishiro** 和一个散列，我将使用 john 来破解这个散列

首先，我们把散列放在一个文件中

![](img/70a2d45e86c2a9a897d4fe1f06ce4aa0.png)

现在我们有了 ssh 凭证

**SSH 和 User.txt**

```
ssh kamishiro@10.10.59.18
```

![](img/605a79900985451f1a5abcb06f57eb56.png)

# 私人村庄

![](img/077cd184e723a506ae66481213a1d8ea.png)

我们以 root 用户身份执行 jail.py，让我们看看这是什么

![](img/2537293ebecba2e27d08167e72c9306f.png)

脚本不允许我们执行读取内部文件的命令，我们不能编辑这个文件，我们应该使用内置函数使用[来读取根标志:](https://docs.python.org/3/library/functions.html)

```
__builtins__.__dict__['__IMPORT__'.lower()]('OS'.lower()).__dict__['SYSTEM'.lower()]('cat /root/root.txt')
```

![](img/a102442cb0b0a031efc39bdd9e0cccc2.png)

嘣！我们有根旗

你可以在:
**LinkedIn:-**[https://www.linkedin.com/in/shamsher-khan-651a35162/](https://www.linkedin.com/in/shamsher-khan-651a35162/)
**Twitter:-**[https://twitter.com/shamsherkhannn](https://twitter.com/shamsherkhannn)
**Tryhackme:-**[https://tryhackme.com/p/Shamsher](https://tryhackme.com/p/Shamsher)

[![](img/09e5bbba06c7688a702aeec8570d243c.png)](https://tryhackme.com/p/Shamsher)

如需更多演练，请在出发前继续关注…
…

**点击此处加入电报**

[![](img/149e778f03405fe40fb89bb9dbc5034d.png)](https://t.me/tryhackme_writeups)

[https://t.me/tryhackme_writeups](https://t.me/tryhackme_writeups)

访问我的其他演练:-

感谢您花时间阅读我的演练。如果你觉得它有帮助，请点击👏按钮👏(高达 40 倍)并分享
它来帮助其他有类似兴趣的人！+随时欢迎反馈！