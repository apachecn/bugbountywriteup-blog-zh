# CSRF 真的死了吗？检查条纹的 5000 美元 CSRF 虫赏金。

> 原文：<https://infosecwriteups.com/is-csrf-really-dead-examining-stripes-5000-csrf-bug-bounty-37bbd6631de3?source=collection_archive---------2----------------------->

## 为 CSRF 做测试是值得的。

# 什么是 CSRF？

[CSRF(发音为 sea-surf hehe)，或者更确切地说是客户端请求伪造](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#cross-site-request-forgery-prevention-cheat-sheet)，曾经是 web 应用程序可能经历的最危险的攻击之一。本质上，当用户登录并通过身份验证时，web 应用程序通过使用 cookies 来设置有效的会话状态。当客户端向 web 应用服务器发送请求时，服务器将首先对 cookies 进行身份验证，以验证客户端在执行操作之前可以执行它请求的任何操作。默认情况下，cookies 会随大多数 [HTTP 请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)一起发送，甚至会跨其他站点发送。在 CSRF 攻击中，攻击者让登录的受害者访问一个恶意网站，该网站将向假装受害者的 web 应用程序发送请求。例如，登录到 bank.com 的用户 A 将访问 attacker.com，后者向银行服务器(使用用户 A 的凭据)发送 POST 请求，将 1000 美元从 A 的帐户转移到攻击者的帐户。因为请求包含授权的 cookies，所以传输是有效的。

![](img/3fc5eed57c0e3988ab6ad0fd61bcbcd3.png)

人工智能生成的图像“条纹标志悲伤插图”在 craiyon.com

# 为什么会死？

一个简单的谷歌搜索“csrf 死了吗”,就会发现许多文章和博客都在谈论 CSRF 如何基本上不是一个问题。OWASP(开放 Web 应用程序安全项目)在他们的 [2007](https://github.com/owasp-top/owasp-top-2007) 、 [2010](https://github.com/owasp-top/owasp-top-2010) 和 [2013](https://github.com/owasp-top/owasp-top-2013) 列表中将该攻击列为十大最严重的安全风险，但在他们的 [2017](https://github.com/owasp-top/owasp-top-2017) 列表中，CSRF 不见了。*注:这 4 份榜单是 2007 年至 2017 年间发布的仅有的 4 份榜单。*

这在很大程度上是因为更新的 web 框架、内容管理系统的使用以及 CSRF 防范技术的使用比以前更加普遍。一种常见的保护机制是使用现时令牌。任何可以改变 web 应用程序状态的请求都将具有唯一的 nonce 令牌，该令牌将由服务器在每个请求或每个会话的基础上加密生成，并且该令牌必须在客户端请求和服务器上都匹配才能进行验证。此外，较新的浏览器版本开始设置默认保护，防止[cookie 被转发](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)到外部网站。

![](img/27868c2efbba618c9c25b5ec4180f27a.png)

在 craiyon.com，人工智能生成的图像“在饼干上冲浪”

然而，即使在 2022 年，大公司的 CSRF 漏洞也会获得重大漏洞奖励。

# 条纹的 5000 美元 CSRF 虫赏金

[Stripe](https://stripe.com/) 是互联网上最大的支付平台之一。它是最著名的软件之一，使用 API 来管理在线交易。亚马逊，Lyft，谷歌，甚至 Medium 都用 Stripe。

2022 年 2 月，两个独立的 HackerOne 用户`d_sharad`和`rodolfomarianocy`都向 Stripe 的安全团队报告了同一个 bug。在 Stripe Dashboard 上，有一个 CSRF 漏洞，允许攻击者从不同的站点触发修改用户 Stripe 帐户的请求。这是由于代码更改，在部署时会停用仪表板的 CSRF 保护机制。摘要并没有准确指出可以进行哪些修改，但 Stripe 表示，该漏洞不允许攻击者获取用户数据。此外，由于其严重性等级较低，我认为攻击者可能会对帐户进行重大更改，比如更改密码。由于深度防御机制，如要求重新输入验证码和密码，支付受到了额外的保护。

每个 HackerOne 用户获得 2500 美元奖励，bug 奖金总计 5000 美元。Stripe 证实，实际上没有帐户受到此漏洞的攻击，尽管此漏洞对帐户造成的损害很小，但它代表了其他连锁漏洞的潜在攻击媒介，可能会提高攻击的严重程度。看来 CSRF 并不一定已经死了。

感谢您通读，请在下面留下任何建设性的反馈、建议或问题！如果你喜欢，请考虑在 Medium 上跟随我或者[请我喝杯咖啡](https://www.buymeacoffee.com/robertocyberkid)。在 roberto.cyberkid@gmail.com 联系我，在[推特](https://twitter.com/CyberKidLama)关注我，在 [LinkedIn](https://www.linkedin.com/in/roberto-lama-9a126a123/) 联系我！