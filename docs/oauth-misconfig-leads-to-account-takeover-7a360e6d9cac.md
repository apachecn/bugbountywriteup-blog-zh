# Oauth 错误配置—导致帐户被接管

> 原文：<https://infosecwriteups.com/oauth-misconfig-leads-to-account-takeover-7a360e6d9cac?source=collection_archive---------0----------------------->

> 你好，信息安全社区，

这是我第一次为我报告的漏洞写文章，并获得了第一笔 bug 奖金。

开始吧:)

![](img/64ec5193adf3d646f2a69bac7e33d300.png)

Studiosoyuz

## 我是如何找到目标的？

我是一个兼职的 bug 猎人，喜欢在 web 应用程序上寻找 bug。经过大量的重复，不适用于 bug 搜索平台，我决定在竞争较少的 RVDP 程序上搜索。我报告了一些错误，收到了一些感谢邮件，也没有多少人因为保护应用程序而进入名人堂。

一段时间后，我开始随机搜索网站，就像我们在日常生活中使用一些网络应用程序一样。我在那些甚至没有 RVDP 程序或任何安全团队的网站上练习。我向他们报告了这些 Bug，但是众所周知，许多公司都没有任何回应——挣扎 Bug 猎人的脸。但是仍然有公司尊重 bug hunters❤️

我选择了一个这样的网站，并开始基本的扫描和侦察。我将使用 example.com 作为网站名称。

![](img/bf625574c37f1549e2ea907b29e9d6f7.png)

我发现 example.com 有一个注册方法

1.  Gmail 和设置密码
2.  OAuth 配置错误

我开始检查 OAUTH Bug。

**OAUTH 是什么？**

OAuth 是一种开放标准的授权协议或框架，它描述了不相关的服务器和服务如何能够安全地允许对它们的资产进行经过身份验证的访问，而无需实际共享初始的、相关的单一登录凭证。在认证术语中，这被称为安全、第三方、用户代理、委托授权。

简而言之，OAuth 是一个点击式流程，所有最终用户和安全研究人员都可以轻松注册。

example.com 使用推特、脸书、谷歌和苹果 Oauth 登录网站。作为受害者，我通过 Google sign in 注册并登录了该应用程序。

**重现步骤**

1.  用受害者的名字、电子邮件地址注册一个账户，并设置密码。
2.  现在，您可以通过您设置的电子邮件 id 和密码访问受害者的帐户。
3.  在不知情的情况下，受害者将通过 Google OAuth 功能创建一个帐户。
4.  因此，受害者不需要设置密码。

**漏洞利用场景**

您可以通过在攻击者阶段设置的密码访问受害者帐户。攻击者可以更改任何设置，如果网站有任何溢价或支付细节，会导致敏感信息泄漏。攻击者可以随时使用受害者帐户。

我做到了，并向 example.com 报告了同样的情况。安全团队对这一发现表示赞赏，并宣布悬赏金额为 100 美元。

![](img/c12c3fdd23faca33e33b75c003e5531e.png)

有许多方法可以执行 OAuth 攻击。这是一个简单且非常容易利用的方法。

# **时间线**

1 月 16 日—报告了错误

1 月 25 日—接受了 Bug

2 月 9 日—收到 100 美元奖金

***感谢您抽出时间*** 。问候[拉克什](http://www.rakeshelamaran.tech)

我很快会写更多我的发现，所以，请关注我的下一篇文章。

查看我的博客——我是如何获得[网络安全工作](https://rakeshelamaran.medium.com/how-i-landed-in-cybersecurity-job-my-journey-9dca65e224ed)的

留个掌声 [***关注***](https://medium.com/@rakeshelamaran) 获取更多更新。

***与我联系:***

https://www.linkedin.com/in/rakeshelamaran98/

https://twitter.com/rakeshoffcl