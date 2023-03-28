# 跨站点请求伪造😈

> 原文：<https://infosecwriteups.com/cross-site-request-forgery-ead42933972d?source=collection_archive---------1----------------------->

## CSRF 是什么，它是如何做到的，以及如何防止它

![](img/f981798dd1ea36c0e20d8353557266c9.png)

迈克尔·盖格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

跨站点请求伪造(简称:CSRF 或 XSRF)是一种攻击，它使受害者的浏览器向受害者拥有有趣权限的网站执行请求。它有时发音为“海上冲浪”或被称为“会议骑行”。CSRF 攻击可能会让您的浏览器从您的银行向攻击者转账，在网上商店为攻击者买东西，连接到社交网络，如产品/推文/帖子，以及许多其他东西。

让我们学习 CSRF 是怎么做的，以及如何防止它！

# 为什么你应该关心

类似于 SQL 注入，如果您知道 CSRFs 是一个问题，您可以很好地防御它。但是，如果您没有意识到这个问题，您可能也不会采取任何措施来应对攻击。

*   CSRF 在 2013 年 OWASP 十大排名中名列第八，在 2010 年排名第五。
*   2008 年:网飞易受 CSRF 袭击([来源](https://seclists.org/fulldisclosure/2006/Oct/316))
*   2016 年:AVM·弗里兹博克斯易受 CSRF 袭击([来源](https://www.zdnet.de/88256320/avms-fritzbox-router-mit-aelterer-firmware-von-sicherheitsluecke-betroffen/))
*   2019: [www.cert-bund.de](http://www.cert-bund.de) 易受 CSRF 攻击([来源](https://www.golem.de/news/websicherheit-cert-bund-war-anfaellig-fuer-csrf-angriff-1910-144699.html)、[来源 2](https://www.cert-bund.de/freetextmessage/CB-I19-0001) )。这是德国政府的一个与 IT 安全有关的组织。
*   2019 年:戴尔的 SupportAssist 易受 CSRF 攻击([来源](https://threatpost.com/dell-flaws-security-support-tool/144295/))
*   2020 年:奥地利移动互联网提供商(Drei，Yesss)易受 CSRF 攻击([来源](https://www.heise.de/news/rC3-Massive-Schwachstellen-in-Apps-von-Mobilfunkanbietern-4986008.html))
*   2020 年:Meetup.com 易受 CSRF 攻击

# 它是如何工作的

网站需要一种认证方式。当您登录时，您的浏览器会存储一些会话信息，以防止您在每次请求时都必须输入用户名和密码。它通常存储为 cookie。我们姑且称这部分为“会话上下文”。每次浏览器发出请求时，都会自动发送会话上下文。

我们来做个例子攻击。你是 bank.com 的忠实顾客。你几乎一直都在登录。攻击者创建了一个恶意网站 attack.com，并诱骗您访问它。当你访问 attack.com 时，你的浏览器会自动执行上面的 JavaScript。它还下载所有图像。攻击者告诉您的浏览器调用

```
bank.com/send_monkey?to=attacker&amount=1234.56
```

当你登录并成为 bank.com 的客户时，你刚刚损失了 1234.56 美元。这就是这个名字的由来:攻击者让受害者的浏览器创建一个请求。这个**请求是在 attack.com**伪造的，但是这个请求却被寄到了 bank.com。请求是**跨域**。

攻击者如何让浏览器调用域？

**图像标签**是 GET 请求的一个选项:

```
<img src="bank.com/send_money?to=attacker&amount=1234.56" />
```

**表单**是 GET 或 POST 请求的另一个选项:

```
<iframe style="display:none" name="iframe"></iframe>
<form method="POST" action="[http://vulnerable-bank-953.com](http://vulnerable-bank-953.com)" target="iframe" id="form">
    <input type="hidden" name="to" value="attacker" />
    <input type="hidden" name="amount" value="1000" />
</form>
<script>document.getElementById("form").submit()</script>
```

上传和删除请求更难，但是如果服务器有一个 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 错误配置，这是可能的。

## CSRF 登录攻击

CSRF 登录攻击超级怪异。攻击者注销受害者，并确保受害者使用攻击者的帐户登录。

你可能认为这只会伤害攻击者，但这在很大程度上取决于网站。YouTube 频道 LiveOverflow 展示了一个可怕的方式，如何利用针对你自己账户的 [XSS 攻击](https://levelup.gitconnected.com/cross-site-scripting-xss-fd374ce71b2f)+注销 CSRF +登录 CSRF 来窃取用户密码:

# 我如何防御 CSRF 的攻击？

有几个方法是可行的，还有一些方法看似合理，但实际上并没有帮助。对 CSRF 起作用的防御标有\\,其他标有❌.

## 反 CSRF 代币

您可以向每个表单添加一个随机值。当提交表单时，值也被传输。然后，后端需要知道哪个随机值是预期的，并进行验证。如果值不匹配，请求将被阻止。

这被称为同步令牌模式(STP)。阅读 Jude Niroshan 的精彩文章，了解更多信息:

[](https://medium.com/@jude.niroshan11/what-is-csrf-synchronizer-token-pattern-3b244791ab4b) [## 什么是 CSRF 同步器令牌模式？

### CSRF 或跨站点请求伪造是 OWASP 安全风险中列出的一种众所周知的安全攻击。CSRF 是…

medium.com](https://medium.com/@jude.niroshan11/what-is-csrf-synchronizer-token-pattern-3b244791ab4b) 

## 相同站点 cookie \u 000

将`[SameSite](https://developer.mozilla.org/de/docs/Web/HTTP/Headers/Set-Cookie/SameSite)` [cookie 属性](https://developer.mozilla.org/de/docs/Web/HTTP/Headers/Set-Cookie/SameSite)设置为`Lax`甚至`Strict`有助于防止 CSRF 攻击。这个想法是告诉浏览器，只有当请求来自同一个站点时，才应该发送 cookie。

所有相关浏览器都支持 SameSite 属性[。](https://caniuse.com/same-site-cookie-attribute)

## 不记名代币

只有在浏览器自动验证的情况下，CSRF 才是可能的。对于不记名令牌(JWT)，应用程序必须创建该报头。所以 CSRF 是不可能的([来源](https://goo.gl/maps/p24NDNXrViwPxdh6A))。

## 仅接受通过❌发布请求进行的状态更改

攻击者可以在 evil.com 页面上添加一个带有`method=POST`的表单。表格通过页面上的`onclick`链接提交。

## 多因素认证❌

MFA 或 2FA 只有在您必须为每个请求都这样做时才有帮助。然后我宁愿称之为授权机制。

如果你的银行希望每笔交易都有一个 TAN，这很有帮助。如果你的银行只是要求你使用智能手机上的应用程序登录，那就没什么用了。攻击的目标是请求，而不是登录。

## 同源政策❌

[同源策略](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)阻止嵌入了 iframe 的页面读取嵌入页面的 cookie，除非它们来自同一个源。

同源策略(SOP)的目的是防止数据泄露。读取数据被阻止，但发送数据不受影响。

## 参考资料-标题❌

由于隐私的原因，引用头可能会被浏览器删除。因此，过滤引用头可能会影响普通用户。

## 作为用户…

…使用完一个帐户后，您可以注销。

您也可以使用一个浏览器来显示可信的内容，而使用另一个浏览器来显示不可信的内容。但是说实话:谁会真的那么做呢？

# 请参见

YouTube 频道 [PwnFunction](https://www.youtube.com/channel/UCW6MNdOsqv2E9AjQkv9we7A) 包含一个很棒的视频，总结了这个话题。 [OWASP](https://owasp.org/www-community/attacks/csrf) 和 [PortSwigger](https://portswigger.net/web-security/csrf) 也包含好文章。

# 下一步是什么？

在这个关于应用安全(AppSec)的系列文章中，我们已经解释了攻击者的一些技术😈以及防守队员的技术😇。我们还讨论了 OWASP 前 10 名的部分内容🐝：

*   第 1 部分: [SQL 注入](https://medium.com/faun/sql-injections-e8bc9a14c95)😈🐝
*   第二部:[不要泄露秘密](https://levelup.gitconnected.com/leaking-secrets-240a3484cb80)😇
*   第 3 部分:[跨站脚本(XSS)](https://levelup.gitconnected.com/cross-site-scripting-xss-fd374ce71b2f) 😈🐝
*   第 4 部分:[密码哈希](https://levelup.gitconnected.com/password-hashing-eb3b97684636)😇
*   第五部分: [ZIP 炸弹](https://medium.com/bugbountywriteup/zip-bombs-30337a1b0112)😈
*   第六部分:[验证码](https://medium.com/plain-and-simple/captcha-500991bd90a3)😇
*   第 7 部分:[电子邮件欺骗](https://medium.com/bugbountywriteup/email-spoofing-9da8d33406bf)😈
*   第 8 部分:[软件组成分析](https://medium.com/python-in-plain-english/software-composition-analysis-sca-7e573214a98e) (SCA)😇🐝
*   第九部分: [XXE 袭击事件](https://medium.com/faun/xxe-attacks-750e91448e8f)😈🐝
*   第十部分:[有效的访问控制](https://levelup.gitconnected.com/effective-access-control-331f883cb0ff)😇🐝
*   第十一部分: [DOS via 十亿次大笑](https://medium.com/bugbountywriteup/dos-via-a-billion-laughs-9a79be96e139)😈
*   第十二部分:[全磁盘加密](https://medium.com/faun/full-disk-encryption-2090489f9760)😇
*   第 13 部分:[不安全的反序列化](https://medium.com/bugbountywriteup/insecure-deserialization-5c64e9943f0e)😈🐝
*   第 14 部分:[码头工人安全](https://levelup.gitconnected.com/docker-security-5f4df118948c)😇
*   第十五部分: [CSRF](/cross-site-request-forgery-ead42933972d) 😈🐝

这即将到来:

*   磁盘操作系统😈
*   雷多斯😈
*   凭据填充😈
*   密码劫持😈
*   单点登录😇
*   双因素认证😇
*   备份😇

如果您对更多关于 AppSec / InfoSec 的文章感兴趣，请告诉我！