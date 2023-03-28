# 15 分钟内 350 美元 XSS

> 原文：<https://infosecwriteups.com/350-xss-in-15-minutes-dcb74ad93d5f?source=collection_archive---------0----------------------->

## 通过 JSONP +参数污染写关于多姆 XSS 的 Bug Bounty

![](img/dc5ec1e485f06d7ad72e696ba45d35a0.png)

照片由 [Pepi Stojanovski](https://unsplash.com/@timbatec?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

你好👋

这是我今年的第一篇也是最后一篇关于 Bug Bounty 的文章。😀

我正在与你分享我最新的 XSS 发现，这是我两周前发现的。

这是最快的，有点不寻常的流量，我通常这样做时，我搜索 XSS。

所以让我们开始吧…

*   公司让我重新测试一份旧的 XSS 报告。
*   我已经检查了 XSS，并确认它已被正确修复。
*   特定终点的参数`name`易受反射 XSS 注入的影响。

```
example.com/profile?name=<img+src=1+onerror=alert(1337)>
```

*   我已经开始搜索一个旁路，并使用 Chrome Developer tools 中的搜索功能在所有 JS 文件中搜索这个端点`/profile`，以检查另一个易受攻击的参数，但找到了另一个端点:

```
example.com/services
```

*   我想到的第一个想法是把这个 URL 放在 google 搜索引擎中，看看这个端点是否用 params 缓存在 google web 空间的某个地方。
*   在第一次尝试后，我在结果的第一页上发现了一个缓存的端点，该端点有 ID 参数和一些其他参数。

```
example.com/services?id=123&page=Demo
```

*   我已经将我的有效载荷`qwe'"<X</`添加到 ID 参数中，并开始检查网页源代码中是否有任何反映。

```
example.com/services?id=123qwe'"<X</
```

*   除此之外，我还打开了 Chrome Developer tools 中的 Network 选项卡，检查这个端点可能发送到某个地方的所有请求。
*   在第二次刷新页面后，我发现了一个有趣的 AJAX 请求，它使用了 JSONP 回调参数和来自端点本身的 ID 参数。AJAX 请求 URL 与此类似:

```
lib.com/find?id=123qwe&jsonp=cb12
```

*   我测试的第一件事是 JSONP param 本身，看看我是否能把它变成一个带有自定义参数的`alert`函数
*   令我惊讶的是，没有检查 JSONP 值，所以我很容易地将其更改为`alert(1337);`
*   现在是时候再次检查 ID param，看看它是否接受其他符号，例如，`%` sign 来创建一个编码的有效负载，以便向 AJAX URL 添加自定义参数。
*   我已将端点 URL 更改为

```
example.com/services?id=1%26jsonp=alert(1337);%23
```

*   JS 处理的时候把`%26`转换成&把`%23`转换成#。浏览器会忽略# (hashtag)符号后面的所有内容。最终的 AJAX 调用如下所示:

```
lib.com/find?id=1&jsonp=alert(1337);#&jsonp=cb12
```

*   使用这个 AJAX URL 操作(参数污染攻击)，我成功地触发了一个文本为`1337`的警告框。这证实了多姆 XSS 漏洞的存在，我收到了 350 美元的奖金，另外 50 美元用于重新测试一份旧报告。

感谢阅读！

*继续阅读……*

🔗[如何找到你的第一个 Bug:Bug 赏金的动机和技巧](https://therceman.medium.com/how-to-find-your-first-bug-motivation-and-tips-for-bug-bounty-hunting-5e7343066d0c)
🔗[如何开始 Bug 赏金狩猎](https://therceman.medium.com/how-to-start-bug-bounty-hunting-94b1ff3dda27)

又及:我正在写一本给 Bug Bounty 世界初学者的书。这本书将包括网络，HTML 和 JavaScript 基础知识，广泛的漏洞的简短描述，以及对 XSS 漏洞的深入分析，包括示例，提示，工具和技巧。在本书的最后，我将教你如何创建和部署你自己的 NodeJS 服务来测试盲 XSS / SSRF 漏洞。

请继续关注更新，不要忘记至少在某个地方订阅，这样你就不会错过任何关于这本书的信息。

节日快乐&新年快乐！

分享臭虫奖励提示

🔸[LinkedIn.com/in/therceman](https://LinkedIn.com/in/therceman)

🔸[Twitter.com/therceman](https://Twitter.com/therceman)

🔸[Telegram.me/therceman](https://Telegram.me/therceman)

🔸[TikTok.com/@therceman](https://TikTok.com/@therceman)

🔸[YouTube.com/therceman](https://YouTube.com/therceman)

最诚挚的问候，安东

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！