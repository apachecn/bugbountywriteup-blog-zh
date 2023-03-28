# 重复但仍然很酷

> 原文：<https://infosecwriteups.com/duplicate-but-still-cool-236835685075?source=collection_archive---------0----------------------->

![](img/c2fb429fa335abe470856ebdc9374a6d.png)

**TL；博士**，从低影响到账户接管再到复制这里是我在 [HackerOne](https://medium.com/u/6f816e37be2c?source=post_page-----236835685075--------------------------------) 的一个私人程序上发现的一个很酷的 bug 的故事。

redacted.com 公司为用户提供 CRM 服务，用户可以注册成为一个组织，然后邀请具有管理员角色或基本用户角色的团队成员。邀请过程一开始看起来没问题，一切都检查得很完美，邀请链接有 32 字节的字母数字令牌(没有办法强制使用)，csrf 令牌检查正确，一切都很好，直到我发现第一个错误

## IDOR 在重新发送邀请时

一个管理员有能力查看待定的邀请，他也可以重新发送邀请，似乎很好的职位请求如下

> POST/invitations/363484/resend HTTP/1.1【bugbounty.redacted.com】主持人:T4
> 授权:无记名 base64 令牌

> JSON 响应包含电子邮件{"status":"success "，" email":"email@example.com"}

问题是，如果你改变邀请 id，你实际上可以看到其他注册在 redacted.com 的公司发出的邀请。创建多个邀请后，我注意到邀请 id 增加，这也证实了没有必要使用暴力简单地使用入侵者和增加/减少 id 来披露其他公司的邀请电子邮件。这本身并不重要，因为除了邮件，我们在回复中看不到任何重要信息。

## 用设计缺陷链接第一个 bug

现在真正有趣的事情开始了，我抓起回复中的邮件，然后在 redacted.com 上注册了一个账户。注册后，我被重定向到一个 html 页面，上面写着

> X 公司邀请您加入他们，请使用电子邮件中的链接。
> 点击重新发送邀请，再次接收电子邮件

复制了页面上的重新发送邀请链接，看起来像这样

> https://www.redacted.com/resend_invitation/TOKEN

我去查看了我的邮件，它有相同的链接，除了他们用了*加入*而不是*重新发送邀请*，所以链接是这样的

> https://www.redacted.com/join/TOKEN

所以我回到标签页，把 resend_invitation 换成了 join 和 boom！我被录取了。攻击者只需利用第一个 IDOR，注册 json 响应中反映的每封电子邮件，就可以利用该漏洞接管任意帐户。

这是一份重复的报告，但很酷。
*给原记者如果你正在读这篇文章那么干得好；)*

下次见，祝大家狩猎愉快，

[增压室](https://medium.com/u/d5bc3c7f04c0?source=post_page-----236835685075--------------------------------)