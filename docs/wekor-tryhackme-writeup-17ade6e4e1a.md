# Wekor Tryhackme 报道

> 原文：<https://infosecwriteups.com/wekor-tryhackme-writeup-17ade6e4e1a?source=collection_archive---------5----------------------->

作者 Shamsher khan 这是一篇关于 Tryhackme room“Wekor”的文章

![](img/e77499c906c6391a67d1bf0123f92b67.png)

https://tryhackme.com/room/wekorra

**房间链接:**[https://tryhackme.com/room/wekorra](https://tryhackme.com/room/wekorra)
**注:此房免费**

将标签添加到主机文件

```
echo "10.10.62.4      wekor.thm" >> /etc/hosts
```

# 列举

![](img/a5d0d922f43d0ff5cc4652fc2e7ce9c6.png)

在端口 80 上

![](img/c3e858bccfb79259b37277defad46b67.png)![](img/66673d869faac17b7d45b39377e7d299.png)

网站上还有一个 robots.txt，在访问 robots.txt 时，我们会看到许多不同的目录路径。遗憾的是，除了一个“/comingrealysoon”之外，所有这些都将我们重定向到 404(没有找到)

![](img/c0394a345571b6347114d6c842c7bbcd.png)

这里我们找到了另一个目录

![](img/2a8007f88ef6d4152a47330a1b97b433.png)

经过一番探索，我们看到在网站的结帐部分有一个表单域，在那里他们要求提供优惠券代码。
测试一个可能的 SQL 注入，试图只放一个单引号，网站反映一个错误信息。

![](img/84869855a20f86f701017e8fb8e57318.png)

使用“**”或 1 = 1—**”，不带双引号，将获得实际的优惠券代码。

使用 Burpsuite 捕获请求

![](img/aada78058cc9e508989fe85273b32b6e.png)![](img/20760aa57ef0c79ea4a0d4c487d5aa56.png)

运行 SQLMAP

> *sqlmap -r request.txt*

这证实了我们有 SQL 注入的可能:

![](img/cc6f85854629bb35ac915420a46105f8.png)

检查可用的数据库:

> *sqlmap-r request . txt—DBS*

![](img/d6cc88e7afb09911fd5831cceadb922d.png)

检查 WordPress 数据库中的表格:

> *sqlmap-r request . txt-D WordPress-tables*

![](img/a17c71e8292927c437b011f7d3b3cb58.png)

转储表“wp_users”

> *sqlmap-r request . txt—dump-D WordPress-T WP _ users*

![](img/e456d808698555de90cc36332b1e0d62.png)

因此，我们有网站用户“管理员”的散列:[http://site.wekor.thm/wordpress](http://site.wekor.thm/wordpress)

从[这里](https://hashcat.net/wiki/doku.php?id=example_hashes)我们可以看到 has 的类型是“phpass”:

![](img/424e9ac60aaf9d07573f26753457ec1d.png)

[https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

把所有的散列放在一个文本文件中，然后用 JTR 破解:

> *John—word list = rock you . txt—format = phpass hash . txt*

![](img/ebf42cb95cbe0849ddba4d990db09bc6.png)

破解了一些哈希，尝试破解用户“wp_yura”的密码，我们可以登录到[http://site.wekor.thm/wordpress/wp-](http://site.wekor.thm/wordpress/wp-)login.php。

![](img/0b5ae78b9dea292bf54563c27eca5e64.png)

# 反向外壳

现在可以从这里通过外观- >主题编辑器- > 404 模板(404.php)注入一个 [php 反向外壳](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)得到一个反向外壳:

![](img/dbcad5f9d2b54c71e31a6718bb81eb02.png)

记得输入我们想要获得反向 shell 的机器的 IP 地址和端口号。还要在该计算机上启动 netcat 会话。现在使用以下链接访问 404.php:

> [*http://site . wekor . thm/WordPress/WP-content/themes/twenty twenty one/404 . PHP*](http://site.wekor.thm/wordpress/wp-content/themes/twentytwentyone/404.php)

我们得到了一个相反的外壳:

![](img/e171d1ba4a7a4403d3cb267d580353a8.png)

我们有哪些用户

```
cat /etc/passwd
```

![](img/19f8afbe5c497626288a9c626f6ef050.png)

只有 Orka 和 root 拥有 shell 配置。

寻找开放端口，您可以在端口 11211 中找到正在运行的东西。

![](img/c5498d14f1258706b65a4d0957791bc9.png)

在谷歌搜索后，我们发现这是一个 memcached 服务器。再进行一些搜索，我们得到了转储缓存数据的命令。

![](img/b5893e46f6af019e8588ef575cc50046.png)

好了，现在我们有 Orka 密码了。

作为奥卡，你能做什么？

![](img/d63a4d6227ba489b8f4ac159d1686f3d.png)

# 权限提升

现在是时候进行权限升级了。首先，在运行任何脚本之前，让我们检查 Orka 是否可以使用`sudo -l`运行任何东西:

![](img/7734ed5ddcf2383668a402fc2a0f7829.png)

可以执行比特币为 sudo。你也不能改变比特币，但你可以改变桌面文件夹。我们把比特币换成 bash，求根。

![](img/d7b0ed2c5a1bb56c354a5a9159d37ac5.png)

你可以在:
**LinkedIn:-**[https://www.linkedin.com/in/shamsher-khan-651a35162/](https://www.linkedin.com/in/shamsher-khan-651a35162/)
**Twitter:-**[https://twitter.com/shamsherkhannn](https://twitter.com/shamsherkhannn)
**Tryhackme:-**[https://tryhackme.com/p/Shamsher](https://tryhackme.com/p/Shamsher)

![](img/09e5bbba06c7688a702aeec8570d243c.png)

如需更多演练，请在出发前继续关注…
…

访问我的其他演练:-

感谢您花时间阅读我的演练。
如果你觉得有用，请点击👏按钮👏(高达 40 倍)并分享
它来帮助其他有类似兴趣的人！+随时欢迎反馈！