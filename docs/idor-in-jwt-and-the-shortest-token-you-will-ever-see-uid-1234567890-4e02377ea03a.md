# JWT 的 IDOR 和你见过的最短的 TOKEN { }。{"uid": "1234567890"}

> 原文：<https://infosecwriteups.com/idor-in-jwt-and-the-shortest-token-you-will-ever-see-uid-1234567890-4e02377ea03a?source=collection_archive---------0----------------------->

**TL；许多大公司都在使用 JWT，但是有些实现并不安全，这是一个让我损失了 1500 美元的错误**

今年早些时候，我在 [HackerOne](https://medium.com/u/6f816e37be2c?source=post_page-----4e02377ea03a--------------------------------) 上参加了一个 bugbounty 项目，这个应用程序允许客户订购产品，它看起来很可靠，我设法找到了一些有趣的角色转换问题，所以我花了几个小时试图获得存储的 xss，但没有运气，所以几天后我有点失望。我关闭了 burpsuite 开始了一个新项目，并决定从头开始。

我输入了地址，比如说 http://example.com 的，然后用我女朋友的账号登录(我不想点任何东西)。登录后，我注意到一个类似于

> OPTIONS / HTTP/1.1
> 主机:customer-order-status.example.net
> 内容类型:应用程序/json
> 连接:关闭

后来，我的女朋友在这个应用程序上订购了一个产品，她可以检查她的订单状态，检查订单请求看起来像这样

> GET/API/v1/order _ status/{ order _ id } HTTP/1.1
> 主机:customer-order-status.example.net
> 授权:无记名 JWT 令牌
> 内容类型:应用程序/json
> 连接:关闭

JSON 响应包含个人账户信息，包括送货地址、订单细节、支付方式、支付金额……所有有趣的东西

JWT 令牌有会话 id 和用户 id，用 HS256 签名，很好，但是 api 没有验证这些

> {"alg": "HS256 "，" typ": "JWT"}。{"uid": "1234567890 "，" sessionid ":"字母数字 ID"}。签名

我最终使用的授权令牌的较短版本如下所示

> {}.{"uid": "1234567890"}

所以我可以使用上面的 JWT 令牌查询这个 api 端点，并且响应是有效的。这在使用过去的订单 id 时也有效

为了让攻击者有效地利用这一点，需要某种程度的暴力来查询随机用户信息，因为事实上有两个变量用户 id 和订单 id 这有点难以利用，但是使这一点变得危险的是服务器没有实现请求限制，因此这可能会使暴力攻击可行。