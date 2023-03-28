# 关于密码重置漏洞的所有信息

> 原文：<https://infosecwriteups.com/all-about-password-reset-vulnerabilities-3bba86ffedc7?source=collection_archive---------0----------------------->

![](img/fdc65e8e1a98ac7c8b468c433115ea86.png)

你好黑客们，

希望你们做得很好，并狩猎大量的昆虫和美元！

今天我们要谈论一些网络安全漏洞，它发生在密码重置功能上。所以今天，我们将看到一个简单的方法和途径来发现这个非常常见的功能中的错误。

大多数 web 应用程序通过电子邮件为用户提供“密码重置”功能。该功能允许用户恢复他们的帐户，生成新的密码，并修复他们自己的问题。所以让我们开始学习如何在这个函数中寻找 bug。

> **密码重置链接未过期**

当用户请求更改密码时，他会得到一个密码重置链接来重置密码，这是正常的行为，但它也应该在一段时间后过期。如果没有过期，并且您可以多次使用密码重置链接来重置密码。那你就当是脆弱吧。

[](https://hackerone.com/reports/685007) [## 黑客上披露的 Imgur:密码重置链接未过期...

### 漏洞:更改电子邮件# # #后密码重置链接不会过期概念证明:1。发送密码重置…

hackerone.com](https://hackerone.com/reports/685007) [](https://hackerone.com/reports/898841) [## HackerOne 上披露的 Shopify:密码重置链接未在过期...

### 您可以使用密码重置链接多次重置您的密码。步骤:1。转到…

hackerone.com](https://hackerone.com/reports/898841) 

> **密码重置无速率限制**

速率限制用于控制进出网络的流量。基本上，没有速率限制意味着没有机制来保护你在短时间内的请求。所以试着发送大量的请求，如果它没有阻止你，那么你可以认为它是一个漏洞。

如何狩猎:-

*   启动打嗝套件并拦截密码重置请求
*   发送给入侵者
*   使用空有效负载

 [## Staging.every.org 在 HackerOne 上披露:重置没有速率限制...

### 摘要:速率限制算法用于检查用户会话(或 IP 地址)是否必须根据…

hackerone.com](https://hackerone.com/reports/838572) 

> **输入长密码时拒绝服务**

通常密码有 8-12-24 位或最多 48 位数字。如果保存密码时没有字数限制，您可以将其视为漏洞。您可以检查当您设置密码，而改变密码或创建帐户作为一个长字符串，这可能导致拒绝服务。

如何狩猎:-

*   启动打嗝套件并拦截密码重置请求
*   发送给入侵者
*   使用空有效负载

[](https://hackerone.com/reports/840598) [## Nextcloud 在 HackerOne 上披露:当...

### 你可以创建一个很长的密码，直到你得到最后一个用户把和 aries 或[DoS]。*通常密码有…

hackerone.com](https://hackerone.com/reports/840598) 

> **密码重置令牌通过推荐人泄漏**

HTTP referrer 是一个可选的 HTTP 头字段，用于标识链接到所请求资源的网页地址。Referer request-header 包含上一个网页的地址，通过该地址可以链接到当前请求的页面。因此，密码重置令牌可能是通过 referrer 请求头泄漏的。

如何狩猎:-

*   请求将密码重置到您的电子邮件地址
*   打开密码重置链接
*   确保不要更改那里的密码
*   在密码重置页面上，点击下面给出的社交媒体链接，并使用 Burp Suite 获取请求
*   检查 referer 标头是否是泄漏的密码重置令牌？

[](https://hackerone.com/reports/751581) [## Nord Security 在 HackerOne 上披露:密码重置链接泄露...

### 报告者发现 web 应用程序在 HTTP referrer 标头中泄漏密码重置令牌。由…

hackerone.com](https://hackerone.com/reports/751581) 

> **通过密码重置页面进行用户枚举**

用户名枚举是攻击者试图从 web 应用程序中检索有效用户名的活动。您可以在登录页面、注册表单页面或密码重置页面检查这种类型的错误。

如何狩猎:-

*   转到密码重置页面
*   输入一个存在的用户名，不会有错误，它将被重定向到登录页面
*   输入一个不存在的用户名，会出现一个错误，比如“用户帐户不存在”等。

[](https://hackerone.com/reports/77067) [## HackerOne 上披露的密钥库:敏感密钥没有速率限制...

### 你好，我注意到一个小的信息泄漏，使得攻击者能够检查一个电子邮件地址是否关联…

hackerone.com](https://hackerone.com/reports/77067) 

> **通过操作邮件参数重置密码**

在为受害者用户请求密码重置链接时，我们可以尝试以下参数操作来获得攻击者电子邮件上受害者重置链接的副本。

*   双参数(又名。HPP / HTTP 参数污染):
    email = victim @ XYZ . TLD&email = hacker @ XYZ . TLD
*   抄送:
    email = victim @ XYZ . TLD % 0a % 0 DCC:hacker @ XYZ . TLD
*   使用分隔符:
    email=victim@xyz.tld，hacker @ XYZ . TLD
    email = victim @ XYZ . TLD % 20 hacker @ XYZ . TLD
    email = victim @ XYZ . TLD | hacker @ XYZ . TLD
*   无域名:
    邮箱=受害者
*   无 TLD(顶级域名):
    email=victim@xyz
*   JSON 表:
    {"email":["victim@xyz.tld "，" hacker@xyz.tld"]}

[https://hackerone.com/reports/1175081](https://hackerone.com/reports/1175081)

> **弱加密问题**

通常，使用 URL 重置密码是一种众所周知的做法，在许多 web 应用程序中都有实现。但是这种方法的不太安全的实现使用带有容易猜测的参数的 URL 来识别哪个帐户正在被重置。

[http://example.com/reset-password?user=victim-user](http://example.com/reset-password?user=victim-user)

因为在这里，用户参数可以更改为任何其他用户名，并在没有适当授权的情况下更改其密码，这可能导致帐户被接管。

因此，web 应用程序会生成一个很难猜测令牌，该令牌将指示密码重置 URL 上的用户名，如
[http://example.com/reset-password?token=a2nb20248130okbbw2a0](http://example.com/reset-password?token=a2nb20248130okbbw2a0)

在 URL 中不应该有关于哪个用户的密码被重置的提示。但是，如果我们因为一个薄弱的加密问题对它进行解码，那么你可以认为它是一个漏洞。

**基本想法就是找出密码重置令牌是如何生成的:-**

*   *基于时间戳生成*
*   *基于密码术生成*
*   *根据用户标识生成*
*   *基于邮件用户生成*
*   *根据名和姓生成*
*   *根据出生日期生成*

[](/bugbounty-how-i-was-able-to-compromise-any-user-account-via-reset-password-functionality-a11bb5f863b3) [## #BugBounty —“让我重新设置您的密码并登录到您的帐户”—我是如何…

### 嗨伙计们，

infosecwriteups.com](/bugbounty-how-i-was-able-to-compromise-any-user-account-via-reset-password-functionality-a11bb5f863b3) 

> **密码重置中毒导致令牌泄露**

密码重置中毒是一种技术，攻击者通过这种技术操纵易受攻击的网站生成指向其控制的域的密码重置链接。这种行为可以被利用来窃取重置任意用户的密码所需的秘密令牌，并最终危害他们的帐户。

如何狩猎:-

*   在 Burpsuite 中拦截密码重置请求
*   在 burp 套件中添加以下标题或编辑标题(逐个尝试)

*你可以使用 ngrok 服务器作为你的攻击服务器*

```
Host: [attacker.com](http://attacker.com)Host: [target.com](http://target.com)
 X-Forwarded-Host: [attacker.com](http://attacker.com)Host: [target.com](http://target.com)
 Host: [attacker.co](http://attacker.com)m
```

然后转发请求，看看你是否得到下面给出的链接，然后你可以认为这是一个漏洞。

【https://ngrok.server/reset-password.php? token = 12345678-1234-1234-12345678901

[](https://hackerone.com/reports/226659) [## 黑客 One 上披露的 concrete5:密码重置链接劫持...

### Summary Concrete5 在发送密码重置链接时使用“Host”报头。这使得攻击者能够插入一个…

hackerone.com](https://hackerone.com/reports/226659) 

希望这对你们有用

黑客快乐！

推特手柄:-[https://twitter.com/Xch_eater](https://twitter.com/Xch_eater)