# 英特尔向上写黑客盒子

> 原文：<https://infosecwriteups.com/intel-writeup-hackthebox-c343bd68a8e9?source=collection_archive---------2----------------------->

[![](img/572699db5c8e0e247b7fd140bdeeaf5e.png)](https://www.tomshw.it/hardware/intel-core-i9-11900k-sara-questa-la-nuova-regina-delle-cpu-da-gaming/)

tomshow.it

嗨伙计们！欢迎回到另一篇文章。

今天我们要解决 intel，一个黑客盒子提出的新挑战。

开始吧！

![](img/7205ca4e1c9364b451c1842fe4b5065f.png)

阅读以上内容

因此，挑战告诉我们，一群名为"**flichsec "**的黑客已经出售了大量信用卡信息，我们的目标是找到任何可以帮助我们发现它们的信息。

![](img/48cf03b50c8a30198608716d35b53026.png)

如果你在谷歌上搜索**flichsec**，第一个结果是一个 LinkedIn 页面，上面有这个简介。

我们试试看他的网站。

![](img/9e3dc8db0c8fd5576cae6fae2b8650cf.png)

他的网站好像关了，也没给我们什么有用的信息。

所以我们能做的就是用 wayback 机器看看有没有保存下来的这个网站的拷贝能帮到我们。

> 注意:wayback machine 是一个保存了过去网站拷贝的网站。

![](img/9ef1bc2a9a802fb5134f72e92481cb0a.png)

让我们看看我们是否幸运…

![](img/f69bd63dbcdb9c0021dbab29864412ad.png)

是啊！！！该网站保存了一份日期为 2020 年 10 月 30 日的副本。

![](img/3dd7f553cffd7b323c688d02b5b1c0a7.png)

网站完全不同，它有一个多汁的 GitHub 链接，让我们看看它包含什么。

![](img/93ba2c9719974fb2aa0c2e4adce4ce41.png)

我们看到这个 GitHub 页面有一个名为**音乐计算机器**的项目，用户有一个加密的名字，也许我们可以解密。

![](img/0261cfe5019b2e5b2cbea75198a7f2cb.png)

不幸的是，哈希无法破解。

让我们更好地检查这个项目。

![](img/7e602d94403f1a0f786713d88b477ac0.png)

这个项目没有任何有用的东西，但它有一个标签，也许这可以帮助我们。

> 注意:标签是项目的保存版本。

![](img/e72d0a68d638daefdc77a5a8395df592.png)

v01 有一个当前版本没有的 exe 文件，让我们下载它。

![](img/7ebeda0622a8925c5a4c4d8dd5ed365b.png)

我尝试的第一件事是使用字符串命令来查看是否有任何人类可打印的信息可以帮助我们，但是没有任何感兴趣的显示…

我尝试的第二件事是对文件进行逆向工程，但这也没有给我们任何有用的信息。

![](img/a95e2fc622bcf317fdd9bdb50c9c0cd5.png)

我尝试的另一件事是检查文件的校验和，看看这个文件是否是恶意软件，这样也许我们可以获得有用的信息。

> 注意:[**校验和**(有时也称为散列)是一个字母数字值，它唯一地表示了**文件**的内容。](http://a2hosting.com)

![](img/dcf3e65d2df35cd91f2889e645f9202a.png)

我们可以使用[病毒总量](http://virustotal.com)来做到这一点。

![](img/a61c5808e8522e6259c383720f489caa.png)

这个文件是木马！😱

如果我们去的细节，看到木马的名字，我们终于得到了旗帜！🙌

![](img/afe21b22d5d1a87821cd169a2c1181dc.png)

感谢你们阅读我的故事，希望对你们有用。

再见！