# 网络缓存中毒:链接未加密输入的故事

> 原文：<https://infosecwriteups.com/web-cache-poisoning-a-tale-of-chaining-unkeyed-inputs-6e3cb026bd23?source=collection_archive---------0----------------------->

你好，猎人们，我希望你们都做得很好，每天都在学习新的东西:)。我今天写的是由[@白化蜡](http://twitter.com/albinowax)解决的一个[实验室/任务](https://hackxor.net/mission?id=8)。

这一次我选择在 deep 学习 web cache 中毒，那么还有什么比 portswigger 更好的地方开始呢&我刚刚从 James(albinowax)发布的关于这一问题的第一篇研究论文开始。

我甚至还没有完全完成这个话题，但是在读完这篇文章后，最终会有一个挑战。完成后，我想我得写点什么。这东西太棒了。

*免责声明:读完这篇文章后，你可能会认为你没有从中学到任何东西。很好，你不应该从这个博客中学习。我希望你自己去了解网络缓存中毒，然后自己解决这个挑战，如果遇到困难，就使用这个指南。*

无论如何，我们非常欢迎你继续阅读

**什么是 web 缓存中毒？**

![](img/709de24325c3c3e5263c29080e1adb76.png)

作者:cpdos.org

要理解这一点，您需要了解什么是缓存以及它是如何工作的？

现在，当您在浏览器中查询某些内容时，您的浏览器会首先在本地缓存中查找相同的请求，如果没有，则将其转发到 DNS 服务器，同样，它还会在其本地缓存中查找其他人是否请求了相同的内容，如果没有，则将其转发到相应的服务器。现在，这些服务器不会直接与您对话，它们在客户端和后端之间还有一个缓存服务器。现在，这个缓存服务器将决定是否需要将请求转发到主服务器，并且它已经有了您的请求的副本。如果没有，您的回答将被再次保存，以供将来参考。

回答将被发送给您，并保存在每一级缓存中。

1.  您的浏览器缓存是本地缓存
2.  DNS 级别缓存是 DNS 缓存
3.  应用程序级缓存是由服务器创建的。

因此，当我们谈论 web 缓存中毒时，不要将其与本地或 DNS 缓存混淆。我们的目标是污染主服务器的缓存，这样恶意内容就可以提供给合法用户，他们查询的请求与我们的相同。

我建议您仔细阅读这篇研究论文或 portswigger 内容，了解它是如何工作的。我向你保证这里有你需要知道的关于缓存中毒的一切。服务器如何决定两个请求是相同的？如何缓解这些问题？如何发现并利用它？也许我以后会发布一个关于它的详细博客。但是这个是关于挑战解决方案的。

[](https://portswigger.net/research/practical-web-cache-poisoning) [## 实用的 Web 缓存中毒

### Web 缓存中毒长期以来一直是一个难以捉摸的漏洞，一个“理论上”的威胁，主要用于吓唬开发人员…

portswigger.net](https://portswigger.net/research/practical-web-cache-poisoning) 

**任务:**

 [## 使命:定制分析

### 奖励 4600 美元客户约翰·多伊建议之前的经验中等嗨，我们是博士生进行一些研究…

hackxor.net](https://hackxor.net/mission?id=8) 

现在的目标是在 transparency.hackxor.net/contact 页面上提供 analytics.hackxor.net 的内容。

param miner 是一个 burp 扩展，用于暴力破解对应用程序响应有某种影响的标头。你可以从 BApp 商店安装它。

在对 transparency.hackxor.net/contact 页面的标题进行暴力处理后，我们发现它支持 X-original-Original 标题。这意味着这个头可以覆盖所请求的路径。

> GET /admin HTTP/1.1
> 主机:unity.com
> 
> HTTP/1.1 403 禁止
> ……
> 访问被拒绝

— — — — — — —

> GET /anything HTTP/1.1
> 主持人:unity.com
> X 原文网址:/admin
> 
> HTTP/1.1 200 OK
> ……
> 请登录

这意味着如果我们要求，我们可以

> 获取/联系 HTTP/1.1
> 
> 主持人:transperncy.hackxor.net
> 用户代理:Mozilla/…..
> Referer:[https://hackxor.net/](https://hackxor.net/)
> 连接:关闭
> Cookie: ………..
> X-original-URL:/任何东西

它不会给 transparency.hackxor.net/contact 回复，但会给 transparency.hackxor.net/anything·佩奇。显然是 404，因为/任何东西都不存在。

所以我们得到了第一个提示，如果我们用 x-original-url 头查询/联系，这个新路径的响应将被缓存在服务器的缓存中。

现在经过一点来回我们会找到一页 transparency.hackxor.net/blog。查看它的源代码，我们看到它从 transparency.hackxor.net 获取了一个 looking。

```
<img src="https://transparency.hackxor.net/static/logo.png"/>
```

所以我们得到了我们的东西，用 x-original-url 查询/contact 页面:/blog 会在/contact 页面缓存/blog 页面的响应。因为服务器已经确定，无论何时有人在主机 transparency.hackxor.net 上查询/联系页面，它都是与之前在其缓存中相同的请求。

所以我们的东道主在我们/博客页面的回应中得到了反映。现在几点了？现在是参数挖掘时间。

运行它会给出一个/blog page 支持的头，这就是 x-host 头。现在，在这个挑战中，X-Host 正在覆盖主机标头。

因此，请求/博客

> GET /blog HTTP/1.1
> 主持人:transparency.hackxor.net
> X-主持人:analytics.hackxor.net

它在获取徽标时用新主机返回一个响应。

```
<img src="[https://analytics.hackxor.net/static/logo.png](https://analytics.hackxor.net/static/logo.png)"/>
```

现在我们得到了所有需要的材料

让我们将这两个未加密的输入链接起来(头会影响响应，但在缓存时不会被服务器处理)

1.  我们将请求/联系主机:transparency.hackxor.net，这样我们的恶意响应将提供给具有相同路径和主机的用户。
2.  我们将用 X-original-URL : /blog 覆盖/contact 路径。所以 response 返回/blog 页面的响应，而不是 contact。

现在这个响应包含一个 url 来获取一个带有 host:transparency.hackxor.net 的徽标，

3.我们将使用 X-host 报头将主机更改为 analytics.hackxor.net

4.因此，最终的回应将包含 analytics.hackxor.net 主机/博客页面的内容

最终有效载荷看起来像。

> 获取/联系 HTTP/1.1
> 主持人:transparency.hackxor.net
> X-主持人:analytics.hackxor.net
> X-Original-URL:/blog

现在需要注意的是，当你向服务器发送请求时，它会注意到请求的路径与缓存中的路径相同，所以它会用缓存进行回复。您实际上没有与服务器通信。所以为了毒化缓存，你必须等到服务器的缓存过期。然后将你的恶意请求发送到服务器，这样服务器会将你的响应保存在缓存中。这更像是和时间赛跑。一个解决方案是重复做这件事，或者用你的有效负载做一个 while curl 请求循环。

> 缓存控制:公共；最大年龄:60 岁；
> 年龄:4 岁

在响应中，您可以看到缓存将过期多长时间，即 60，而您当前处于 4，请等到 60，然后发送您的请求。

在您的响应被缓存后，您会注意到/contact 也通过不同的主机提供/blog 的内容。

任务完成。

Ps:这个你自己试一次。

这是将两个未加密的头链接起来防止缓存中毒的演练，同时给出了关于 web 缓存中毒的基本说明。无论如何，如果你被困在任何地方，欢迎你在 twitter 上 ping 我。 [@Avinashkroy](https://twitter.com/Avinashkroy)