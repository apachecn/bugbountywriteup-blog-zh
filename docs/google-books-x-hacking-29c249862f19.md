# 谷歌图书 X-黑客

> 原文：<https://infosecwriteups.com/google-books-x-hacking-29c249862f19?source=collection_archive---------1----------------------->

![](img/5e3af61038df95547f43a5e1688251dd.png)

最近，我一直在参与开放漏洞奖励计划，主要关注**跨站点搜索攻击** ( [XS 搜索](https://www.owasp.org/images/a/a7/AppSecIL2015_Cross-Site-Search-Attacks_HemiLeibowitz.pdf))。这篇文章是在 books.google.com 网站上展示成功的跨站点搜索的第一篇。攻击背后的想法来自于在 [35c3ctf](https://archive.aachen.ccc.de/35c3ctf.ccc.ac) 期间提出的[文件管理器](https://youtu.be/HcrQy0C-hEA)任务，该任务基于**滥用铬** [**XSS 审计员**](https://www.chromium.org/developers/design-documents/xss-auditor) **。通过利用这个漏洞，攻击者可以泄露用户的私人藏书以及阅读历史。**

实践中的概念证明

# 脆弱点

在检查页面的源代码时，我注意到了代码源之间有趣的差异。其中之一是当一个搜索查询产生至少一本书时，一个特定的 **JavaScript 代码**被添加，否则就不会被添加。插入的代码以:
`<script>if (window['_OC_registerHover']){_OC_registerHover({"title":"<title>"`开头，其中`<title>`是第一个显示结果的书名。

对于给定的`<title>`和用户的`<uid>`，我能够创建一个链接`https://books.google.com/books?uid=<uid>&num=1&q=<title>&x=<script>if (window['_OC_registerHover']){_OC_registerHover({"title":"<title>"`，当被访问时，窗口将被 *XSS 审计器*阻塞(因为检测到了‘反射’参数`xss`),如果搜索查询的第一个结果有标题`<title>`，否则不会。

另一个或多或少重要的观察是，当页面被 *XSS 审计器*阻塞时，`window.length`保持`0`(这意味着没有内嵌 iframes)。由于谷歌在他们的网站中插入了大量的框架，这个数字通常比`0`要高，因此，通过读取提到的`window.length`属性来检测状态是很简单的，因为它是跨站点可访问的。
*在我合作的 GitHub* [*资源库*](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels) *中可以找到允许检测错误页面的其他方法。*

这种攻击的主要缺点是攻击者必须知道用户的`<uid>`,使得攻击范围变小。为了扩大范围，我仔细研究了`uid=`参数，以下是我的发现:

*   【https://books.google.com/books?uid=vulnerability】T21——**无限重定向到自身。**
*   [https://books.google.com/books?uid=%2B](https://books.google.com/books?uid=%2b)—重定向至`https://books.google.com/books?uid=<uid>&hl=en`。
*   [https://books.google.com/books?uid=%2B1](https://books.google.com/books?uid=%2b1)——抛出 404 回应。
*   [https://books.google.com/books?uid=1%2B1](https://books.google.com/books?uid=1%2b1)—重定向至`https://books.google.com/books?uid=<uid>&hl=en`
*   [https://books.google.com/books?uid='&q =黑客](https://books.google.com/books?uid=%27&q=hack) —重定向到`https://www.google.com/search?tbo=p&tbm=bks&q=hack`
*   [https://books.google.com/books?uid=vulnerability&q = hack](https://books.google.com/books?uid=vulnerability&q=hack)—**显示登录用户的结果，不改变网址**(已修复)

因此，通过将列表中的最后一个漏洞与 *XSS 审计员滥用*结合起来，攻击者可以在不知道受害者的`<uid>`的情况下，对受害者的账户执行成功的*跨站点搜索*。

# 攻击场景

攻击可能在以下情况下进行:

> Google Books 的一个普通用户访问了一个恶意网站。一旦发生任何交互，就会在后台打开一个新窗口，通过操纵 cross-origin `***location***`属性，网页可以轻松利用前面提到的漏洞，从而泄露用户的敏感数据。

被过滤的数据可以包括**搜索的书籍**、**私人藏书**、**购买的书籍、阅读的书籍等信息。**

攻击者可以利用这些信息来**为难**、**勒索**、**威胁**甚至**让受害者承担法律后果**，因为特定的一组书籍在某些国家可能会被禁止([https://en . Wikipedia . org/wiki/List _ of _ books _ banned _ by _ governments](https://en.wikipedia.org/wiki/List_of_books_banned_by_governments))。

# 攻击实施和改进

在一个查询中提供多个“反射”参数是可能的，因此，执行**二分搜索法**算法以获得有效的信息过滤(在我包含的视频中你可能已经注意到了)。在我提交给谷歌的原始报告中可以找到一个概念验证和更详细的攻击描述:[https://terjanq . github . io/Bug-Bounty/Google/books-xs-search-enp GWS 9 jw5mb/index . html](https://terjanq.github.io/Bug-Bounty/Google/books-xs-search-enpgws9jw5mb/index.html)

# 来自谷歌的重要更新

至于我和其他研究人员加剧了*跨站点* *搜索*攻击的工作，谷歌决定通过实施针对这些攻击的一系列防御措施来改善他们的服务。可悲的是，但这一行动的一个自然结果是，他们将不再奖励新的 XS 搜索报告，而是将它们视为重复。

> 因此，该领域的**漏洞报告很可能是重复的**，除非它们显著改变了我们对防御和缓解措施的理解。我们将在该页面发布我们认为已受到适当保护、可抵御 XS 搜索的 web 应用程序和终端，我们还将发放[漏洞研究资助](https://www.google.com/about/appsecurity/research-grants/)来审计我们防御措施的有效性，但在此之前，我们不建议 bug 猎人在这方面花太多时间(以避免重复劳动)。[【1】](https://sites.google.com/site/bughunteruniversity/nonvuln/xsleaks)

# 时间表

*   *2019 年 1 月 27 日晚上 11 点—报道*
*   *2019 年 1 月 28 日上午 10 点 16 分—已分庭*
*   *2019 年 1 月 30 日 02:18AM —已接受*
*   *2019 年 2 月 5 日 06:20PM —奖励 500 美元*
*   *2019 年 3 月 5 日晚上 10 点 47 分—已修复*
*   *2019 年 3 月 19 日 06:20PM —额外奖励 837 美元，总计 1337 美元*

感谢谷歌！

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)