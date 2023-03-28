# Vulnhub 向上写 FristiLeaks

> 原文：<https://infosecwriteups.com/vulnhub-write-up-fristileaks-54b8708f7cbb?source=collection_archive---------1----------------------->

*这是来自*[*VulnHub*](https://www.vulnhub.com/)*的机器*[*fristileaks*](https://www.vulnhub.com/entry/fristileaks-13,133/)*的特写。*

# 摘要

FristiLeaks 机器是一个简单的机器，通过利用上传功能拥有低权限外壳，并在 sudo 的帮助下获得根权限。机器设计的很好，几乎没有兔子洞，对新手来说不错。

> 机器作者: [Ar0xA](https://twitter.com/Ar0xA)
> 机器类型:Cent OS(Linux)

# 专有技术

*   Nmap
*   base64 编码

# 吸收技能

*   bash shell 命令:- *base64，rev，tr*
*   使用 *sudo 进行权限升级。*

# 扫描网络

```
$nmap -sC -sV 192.168.0.174
```

![](img/b6f34f935c7d77f079e99f74271a599d.png)

man nmap

![](img/980999a46c08ef0e9e491aa71398d249.png)

nmap 结果

Nmap 结果显示有 3 个静态页面(*可乐、思思、小熊*)，但是我什么也没找到，我也尝试了目录暴力破解但是没有成功。我试着联系作者，他给了我提示。

![](img/dcb071268f850e52c90c5b3cbde037ad.png)![](img/44ee3d556cb6c75bb9768d1f40b499c3.png)

登录页面

页面源码给出了一些提示，用户名是 **eezeepx** ，他们对图片使用 base64 编码。

# 登录管理门户

![](img/354c73104a9f9b3011367c0a5ed69691.png)

页面源

在页面的底部，有一个用 base64 编码的注释。

![](img/6da998f32749ffcc0d57f1926f13ce98.png)

编码图像

我使用 *base64* 命令将 txt 解码并保存到图像中。

![](img/8c1f268d7d0b3791d5989fdeb9592aef.png)

base64 man

![](img/141651e2ec97220ff61c8f951be8bd4a.png)

解码图像

![](img/8828f89939452a9329455c53661090fb.png)

output.png

> 用户名*:*eezeepz密码 *:* keKkeKKeKKeKkEkkEk

# 低特权外壳

登录后，有上传图像功能。

![](img/7a76b06bbd3560c93bf7b9d38f3ee04b.png)

[http://192 . 168 . 0 . 174/fristi/upload . PHP](http://192.168.0.174/fristi/upload.php)

我试着通过 pentest monkey 上传 PHP 反向 shell，但是只允许使用 ***png，jpg，gif*** 扩展名。

 [## PHP-反向-shell

### 该工具专为 pentest 期间的情况而设计，在这种情况下，您可以上传到运行…

pentestmonkey.net](http://pentestmonkey.net/tools/web-shells/php-reverse-shell) 

在尝试了不同的扩展之后，我使用了双扩展来上传 shell，最终它上传成功了。

![](img/e5c726171bc1f3916e4b983adaac0fb0.png)![](img/0a3a5f59ddb1ad4e167db32593b7615c.png)![](img/3a7e04d0f258e701b956c013136cc0ef.png)![](img/cd34a8265557af803842f63de84633c2.png)![](img/96f77710b59af3bfc7b4f3e90083d0b0.png)

因此，我们可以将命令放在 *tmp* 文件夹中的 *runthis* 文件中，并由 cronjob 以管理员身份运行。

![](img/dc5e5d115ba161ef96b859654be82faf.png)

有趣的文件在 *admin* 文件夹中，所以有 cryptpass.py 脚本，它采用 base64 编码，然后反转它并将每个字母表旋转 13°。

![](img/831ecb072fffd7b0a6ba51c1b70686a5.png)

/home/admin/

我使用 bash 一行程序来解码它，借助于 ***rev*** 和 ***tr*** 命令。

```
echo “mVGZ3O3omkJLmy2pcuTq” | rev | tr ‘[A-Za-z]’ ‘[N-ZA-Mn-za-m]’ | base64 -d
echo “”
echo “=RFn0AKnlMHMPIzpyuTI0ITG” | rev | tr ‘[A-Za-z]’ ‘[N-ZA-Mn-za-m]’ | base64 -d
```

![](img/dcd343b526a502a9eb10dcdda5e11db6.png)

找了一会儿后，我发现***this also pw 123***是 admin 的密码，而 ***是 LetThereBeFristi！*** 是 ***fristigod 的密码。***

# 权限提升

**为*第一次测井后。***

![](img/df1f3af4cec48c6b846bbc84ed622dc6.png)

满须藤

![](img/345036cb44cbb421b1918e9d88253975.png)

# 自己的根

![](img/ea6ae2786b518aaf584fd1cf56c4828b.png)![](img/059d0644c882c34900309c379652cc06.png)![](img/2326479f9e78f40093959c9f3badbff0.png)

根标志

[](https://medium.com/@yashanand155) [## 增量中等

### 从 inc0gnito 介质上读取文字。夺旗类游戏🚩|| HACKTHEBOX || VULNHUB ||反转。

medium.com](https://medium.com/@yashanand155) 

*感谢阅读！如果你喜欢这个故事，请点击**👏 ***按钮，分享*** *它来帮助别人！欢迎留言评论*💬*下图。有反馈？下面我们连线上* [*推特*](https://twitter.com/yashanand155) *。**

# *❤️由[增加到](https://twitter.com/yashanand155)*

**关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。**

*[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)*