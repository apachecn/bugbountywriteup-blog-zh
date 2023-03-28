# 如何操纵反应让我得到一点点，但甜蜜的赏金

> 原文：<https://infosecwriteups.com/how-response-manipulation-got-me-a-little-but-sweet-bounty-38b515ca0910?source=collection_archive---------0----------------------->

## 对你来说也是如此

![](img/71b3e9c73772fade8b84c6a789022aaf.png)

乔希·阿佩尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

大家好，我已经有一段时间没有在 Medium 上写东西了(在这里很忙的几个月)，但是今天我想和你们分享一下反应操纵是如何让我得到一个低严重性的 bug，并伴随着快速获得的奖金。当瞄准一个网站/网络应用程序(或两者都是:D)时，你通常会花一段时间在上面，主要搜索中/高严重性的 bug(有时也是关键的 bug)。然而，有些“简单”的错误有时会让你损失 50 到 200 美元。

似乎好到可以继续读下去？

# 响应操作

当谈到响应操作时，我们谈论的是一种用于让目标显示一些它不应该显示的 UI 元素的技术。它可以用来寻找新的端点、按钮，也可以用来触发一些新的请求。有时，您还可以绕过密码限制或 OTP。要了解响应操作是如何绕过 OTP 的，你可以参考[这篇来自](https://medium.com/bugbountywriteup/otp-bypass-on-indias-biggest-video-sharing-site-e94587c1aa89?source=---------10------------------) [Sriram Kesavan](https://medium.com/u/2ff30b8fc715?source=post_page-----38b515ca0910--------------------------------) 的精彩文章。今天我将谈论一个影响小得多的错误，然而正如我总是说的，它仍然是一个:D 错误:绕过密码限制，在没有任何密码确认的情况下启用 2FA。似乎很愚蠢，为什么攻击者要在受害者的帐户中启用 2FA？此外，这只有在攻击者已经被认证的情况下才有效…我知道，这里的问题是什么！？嗯，就是这个:“违反 Secure Web App 的设计原则”。严重程度较低(显然)。然而，这意外地让我得到了一笔赏金，这可能取决于你是谁举报的。

# “臭虫”

当启用 2FA 时(很快就写了我是如何绕过它的)，它要求密码确认，然后我想为什么不尝试一下所谓的响应操作。

## 工作流程

现在，如何测试呢？这里的问题很简单:您知道是否有可能使用响应操作，然后尝试利用它。但是我怎么知道这是可能的？在我看来，当用于绕过 auth 时，要理解何时可能必须查看您想要绕过的端点是否在完成流程之前触发了另一个操作，或者它是否在 auth 表单之后完成。例如，如果您想绕过 OTP，在输入正确的代码后，它是否会说:“Ok 您已经完成了 OTP 过程，现在登录”。还是问你密码或性别？(举个例子)。如果您遇到第一种情况，那么绕过 OTP 的响应操作将不起作用，但是如果您遇到第二种情况，那么它可能是可行的。在这之后，在尝试响应操作之前，您还需要检查另一个步骤:输入密码或性别之后，代码(或您想要绕过的任何内容)是否还在响应体中？例如，如果您在询问您的性别时点击(例如)男性按钮，出现以下信息:

```
otp=1111&other_data=other_data
```

那么这是不可能的，但是如果你只得到:

```
other_data=other_data
```

然后可以使用响应操作绕过顶层代码。

## 我找到的这个

现在你已经有了足够的关于反应操作的知识来理解下一段的内容。

当启用 2FA 时，它首先要求密码确认(你已经知道:D)。输入正确的密码后，响应如下:

```
HTTP/1.1 200 OK
Date: Tue, 28 Jul 2020 11:30:14 GMT
Content-Type: application/json
Connection: close
Set-Cookie: __cfduid=somecookie; expires=Thu, 27-Aug-20 11:30:14 GMT; path=/; domain=*.redacted.com; HttpOnly; SameSite=Lax; Secure
Cache-Control: no-cache, private
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
Z-Redacted-Password-Auth-Valid-Until: 1595935589
Strict-Transport-Security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="[https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct](https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct)"
Server: cloudflare
Content-Length: 480

{"data":{"type":"createtwofa","attributes":{"secret":"code","qr":"someurl","qr_url":"anotherurl"},"id":"a code(not the password it asked)"},"last_user_action":{"date_iso8601":"2020-07-28T13:30:14+02:00","unix":"1595935814"}}
```

之后，它返回了值和二维码。我用 google-authenticator 扫描了一下，点击了 next，它要求输入验证码，然后确认 2fa 已经启用。使用响应操作尝试绕过密码限制一切正常。

因此，我禁用了 2fa，并试图在它要求密码确认时启用它，但它输入了错误的密码，并得到以下响应:

```
HTTP/1.1 422 Unprocessable Entity
Date: Tue, 28 Jul 2020 11:41:27 GMT
Content-Type: application/json
Content-Length: 177
Connection: close
Set-Cookie: __cfduid=somecookie; expires=Thu, 27-Aug-20 11:41:26 GMT; path=/; domain=redacted.com; HttpOnly; SameSite=Lax; Secure
Cache-Control: no-cache, private
Access-Control-Allow-Headers: origin, content-type, access-token, x-tracking-id
Access-Control-Allow-Origin: *
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-BRedacted-Password-Auth-Valid-Until: 1595935589
Strict-Transport-Security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="[https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct](https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct)"
Server: cloudflare

{"errors":[{"status":422,"code":"user.validation.exception","title":"Validation exception"}],"last_user_action":{"date_iso8601":"2020-07-28T13:41:27+02:00","unix":"1595936487"}}
```

然后我把它改成了这样:

```
HTTP/1.1 200 OK
Date: Tue, 28 Jul 2020 11:30:14 GMT
Content-Type: application/json
Connection: close
Set-Cookie: __cfduid=somecookie; expires=Thu, 27-Aug-20 11:30:14 GMT; path=/; domain=*.redacted.com; HttpOnly; SameSite=Lax; Secure
Cache-Control: no-cache, private
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
Z-Redacted-Password-Auth-Valid-Until: 1595935589
Strict-Transport-Security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="[https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct](https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct)"
Server: cloudflare
Content-Length: 480

{"data":{"type":"createtwofa","attributes":{"secret":"code","qr":"someurl","qr_url":"anotherurl"},"id":"a code(not the password it asked)"},"last_user_action":{"date_iso8601":"2020-07-28T13:30:14+02:00","unix":"1595935814"}}
```

转发并遵循流程，无需有效密码即可启用 2fa。绕过了“安全 web 应用程序设计原则”。

感谢阅读，祝大家好运。