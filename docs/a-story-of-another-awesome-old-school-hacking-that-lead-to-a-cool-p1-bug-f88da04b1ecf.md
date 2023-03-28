# 另一个可怕的老学校黑客的故事，导致一个很酷的 P1 错误

> 原文：<https://infosecwriteups.com/a-story-of-another-awesome-old-school-hacking-that-lead-to-a-cool-p1-bug-f88da04b1ecf?source=collection_archive---------1----------------------->

## 或者响应 200 OK w/ size 0 并不总是表示 0

有时候事情并不总是那么顺利。就像找到一个有 403 的子域，然后对它运行所有的单词列表，这没有任何意义，唯一有趣的是一些大小为 0 的随机端点。当然，你应该试着用 POST request 来对付它，因为也许有什么东西在那里，或者在头上乱搞等等。但是当这不起作用的时候，怎么办呢？嗯，如果星星排列得好一点，而响应大小 0 仍然表示页面主体中没有任何内容，那么像 Server 和 X-Powered-By 这样的响应标题可能会导致很多内容。

**继续黑客之旅—第一部分**

虽然使用各种工具来确定目标正在运行什么非常好，但是没有什么比手动检查目标对您的请求的响应更好的了。最好的方法就是打嗝。无论是免费版本还是付费版本，都提供了基本功能——能够看到完整的响应输出，以及目标配置为公开的所有标头。同样的情况也可以通过其他方式来实现，但是 burp 提供了最具可读性的格式，imho。

所以我碰巧发现了一个随机的目录，ffuf 标记为 200 OK，大小为 0，但是因为我想按照我的其他文章那样用头文件乱搞:

[](/fun-with-header-and-forget-password-with-a-twist-af095b426fb2) [## 有趣的标题和忘记密码，有一个转折:

### 头球是很好玩的东西。最常见的是 X-forward-For(通常用于晶圆旁路)，以及…

infosecwriteups.com](/fun-with-header-and-forget-password-with-a-twist-af095b426fb2) [](/fun-with-header-and-forget-password-without-that-nasty-twist-cbf45e5cc8db) [## 有趣的标题和忘记密码-没有讨厌的扭曲

### 与我的另一篇文章相比，这篇文章没有那种可怕的警告:)在…

infosecwriteups.com](/fun-with-header-and-forget-password-without-that-nasty-twist-cbf45e5cc8db) 

所以，我想通过打嗝访问这个无聊的声音目录，看看我是否可以在弄乱标题的同时实现一些 ssrf 或类似的功能。虽然这并没有产生任何有趣的结果，但我确实注意到响应头中有一些有趣的东西。

![](img/7d519736df984436c72647d865c1b03b.png)

注意这个响应中的 X-Powered-By 头。

**继续黑——第二部分**

这是真正的老式方法。在谷歌上简单搜索一下 JBoss 4 . 0 . 3 Tomcat-5.5 hackerone.com，就能找到有用的文章。致力于此的一个与添加%5C 有关..作为绕过任何 401/403 保护的方法。

![](img/45e66ddf5239c73482a75e538632c2c2.png)

有些修改截图，以保护受影响的一方

这再次显示了手动测试的重要性，即使是那些看起来对他们来说毫无意义的东西。