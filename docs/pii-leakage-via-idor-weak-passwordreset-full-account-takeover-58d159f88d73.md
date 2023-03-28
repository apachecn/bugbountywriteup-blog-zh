# 通过 IDOR +弱密码 Reset =完全帐户接管的 PII 泄漏

> 原文：<https://infosecwriteups.com/pii-leakage-via-idor-weak-passwordreset-full-account-takeover-58d159f88d73?source=collection_archive---------0----------------------->

你好，猎人们，这是一篇关于我最近在一个昆虫赏金项目上的发现的快速报道。在进入漏洞之前，让我们熟悉一些术语。

## **什么是 PII 泄漏？**

个人身份信息( **PII** )是可能识别特定个人的任何数据，如用户名、用户 ID 或任何其他个人信息。PII 泄露就是这种数据的暴露。

## **什么是账户盗用漏洞？**

这是一种允许黑客通过利用应用程序逻辑中的缺陷来完全控制用户帐户的漏洞。

既然程序不允许披露，让我们把程序看作 redacted.com。它始于我开始测试目标的重置密码功能。就像其他任何网站一样，https://redacted.com/forgotpassword 网站上的“忘记密码”也向注册邮箱发送了一封更改密码的邮件。重置密码链接如下:

> [https://redated . com/forget _ password/5 F12 cc 7079 f 273.12051864/](https://droomcredit.com/forgot_password/5f12cc7079f273.12051864/1595067504/MTQ4NzQ5dGFudWpiYXdhcmVAZ21haWwuY29tYXNkZmdoamtsOTE4MjczNzQ2NTAwMA==+++MTQ4NzQ5dGFudWpi+++MTQ4NzQ5)1597479504[/](https://droomcredit.com/forgot_password/5f12cc7079f273.12051864/1595067504/MTQ4NzQ5dGFudWpiYXdhcmVAZ21haWwuY29tYXNkZmdoamtsOTE4MjczNzQ2NTAwMA==+++MTQ4NzQ5dGFudWpi+++MTQ4NzQ5)ntg 4 ntg 4 a2 lsbgvyqgdtywlslmnvbwfzzgznagprbdkxodi 3 mzc 0 nju wmda = +++ ntg 4 ntg 4 a2 lsbgvy ++ ntg 4

即使更改了密码，链接也没有过期。怪异的权利！！。再次请求重置密码时，出现以下链接:

> [https://redacted.com/forgot_password/8ac79ccf2a33.12057854/](https://droomcredit.com/forgot_password/5f12cc7079f273.12051864/1595067504/MTQ4NzQ5dGFudWpiYXdhcmVAZ21haWwuY29tYXNkZmdoamtsOTE4MjczNzQ2NTAwMA==+++MTQ4NzQ5dGFudWpi+++MTQ4NzQ5)1597486704[/](https://droomcredit.com/forgot_password/5f12cc7079f273.12051864/1595067504/MTQ4NzQ5dGFudWpiYXdhcmVAZ21haWwuY29tYXNkZmdoamtsOTE4MjczNzQ2NTAwMA==+++MTQ4NzQ5dGFudWpi+++MTQ4NzQ5)ntg 4 nt G4 a2 lsbgvyqgdtywlslmnvbwfzzgznagprbdkxodi 3 mzc 0 nju wmda = +++ntg 4 nt G4 a2 lsbgvy ++ ntg 4

要注意的是，两个链接的 URL 的最后一部分是相同的。

![](img/53de62cec018a4eb3a1e068f9d3cd690.png)

**分析完上面的链接:**

> 1597486704 → Unix 时间戳

url 的最后一部分是 base64，解码后给出了以下内容:

> 588588 killer @ Gmail . comasdfghjkl 9182737465000 ++ 588588 killer ++ 588588

这里，588588 是我的用户 ID，killer@gmail.com 是我的电子邮件地址。但是等等，那个看起来像胡言乱语的东西是什么[asdfghjkl9156837463000]？

没关系，在玩了一段时间后，我发现只有 URL 的最后一部分，也就是 userID，被服务器验证为密码重置。

> [https://redated . com/forget _ password/5 F12 cc 7079 f 273.12051864/](https://droomcredit.com/forgot_password/5f12cc7079f273.12051864/1595067504/MTQ4NzQ5dGFudWpiYXdhcmVAZ21haWwuY29tYXNkZmdoamtsOTE4MjczNzQ2NTAwMA==+++MTQ4NzQ5dGFudWpi+++MTQ4NzQ5)1597479504[/](https://droomcredit.com/forgot_password/5f12cc7079f273.12051864/1595067504/MTQ4NzQ5dGFudWpiYXdhcmVAZ21haWwuY29tYXNkZmdoamtsOTE4MjczNzQ2NTAwMA==+++MTQ4NzQ5dGFudWpi+++MTQ4NzQ5)ntg 4g 4a 2 lsbgvyqgdtywlslmnvbwfzzgznagprbdkxodi 3 mzc 0 nju wmda = +++ ntg 4 ntg 4a 2 lsbgvy++[VALIDATED _ PART]

所以现在，如果我知道任何用户的 userID，我就可以轻松地更改他的密码。赢？不要。！

现在的目标是找到用户的 UserID 暴露或泄露的地方。经过几天的调查，我能够在一个 javascript 文件中的一个端点上找到一个 IDOR。端点只需要 userID 参数，这会泄露许多敏感信息，如用户名、电子邮件地址，甚至是属于该 userID 的居住地址。

**IDOR 链接:**

> [https://redacted.com/razor/verify_email](https://droomcredit.com/verify_email/148573)？rand =**588588**&request = wcq

现在我所要做的就是通过蛮力枚举每个用户 ID 的电子邮件地址。[PS: UserID 1 属于管理员；)]

**简而言之:**

> 从端点枚举用户 ID 和电子邮件地址→重置密码→使用新密码登录→完全帐户接管

**PS:** 该网站存储了银行账号、PAN、Adhar 卡等个人信息和其他敏感数据，登录受害者账户后即可访问。

谢谢你的阅读！！

与 [Spyder](https://twitter.com/tanujbaware) 合作

**在 Twitter 上关注我**

[*杀手 007*](https://twitter.com/killer007p)

**支持**

[https://www.buymeacoffee.com/killer007](https://www.buymeacoffee.com/killer007)