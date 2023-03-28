# CORS 及其错误配置

> 原文：<https://infosecwriteups.com/cors-and-its-misconfigurations-1654ee04d140?source=collection_archive---------2----------------------->

在了解 CORS 之前，我们需要了解 SOP(同源政策)。SOP 是作为一种安全机制构建的，以保护 web 应用程序不会从另一个网站请求资源。

简单地说，your-website.com 无法从 another-website.com 获得资源。因此，要访问资源，这两个网站必须有`same protocol(HTTP/HTTPS)`、`same domain name`、`same port number(80/443)`。

> 注意:-即使像 api.your-website.com 这样的子域也无权从其根域(your-webiste.com)获取域，因为根据 SOP 的规则，这两个网站有不同的域。

如果您的网站(your-website.com)需要访问 api.your-website.com，那么我们需要为该网站启用/配置 CORS(跨来源资源共享)来访问资源。

要配置 CORS，网站会设置`Access-Control-Allow-Origin`、`Access-Control-Allow-Credentials`等标头。虽然配置 cors 有更多的头文件，但这些是目前广泛使用的方法。

**Access-Control-Allow-Origin:-**这个 Cors 头的值可以是 2 个东西，

1)**another-website.com**:-这里可以有一个特定的网站，告知只有该网站被允许访问资源

2) *:-可以有*表示任何网站无论域、协议、端口都可以访问资源。

**访问-控制-允许-凭证:-** (对/错)第三方网站可以执行特权操作。请将此想象成一个攻击者在进行只有您(经过身份验证的用户)才能进行的更改。

因此，在配置 Cors 时，当开发人员以错误的方式设置这些头时，就会发生错误配置。这种类型的错误配置可能因部署而异。以下是最常见的配置及其相应的风险。

1.  **利用 CORS 标题中配置错误的通配符(*)**

cors 最常见的错误配置是在“Access-Control-Allow-Origin”中使用通配符，这表示任何域都可以访问资源，而与 SOP 的规则无关。

例如:-

*GET /api/userinfo.php
主机:-*[*www.victim.com*](http://www.victim.com) *出处:-*[*www.victim.com*](http://www.victim.com)

当您发送上述请求时，通常会收到如下响应

```
_HTTP/1.0 200 OK 
Access-Control-Allow-Origin: * 
Access-Control-Allow-Credentials: true 
```

所以，在这里作为攻击者，我们可以将 origin 设置为` [https://attacker.com`](https://attacker.com`) 并发送请求。然后，我们将得到与上面相同的响应，因为根据通配符配置，任何域都可以访问资源。

2) **信任域名前通配符作为来源**

CORS 错误配置被利用的另一种常见方式是允许与部分有效的域名共享信息。例如，考虑下面的请求。

*GET/API/userinfo . PHP
Host:provider . com
Origin:requester . com*

对上述请求的响应将是

*HTTP/1.0 200 OK
访问控制允许来源:requester.com
访问控制允许凭证:真*

考虑一下，如果一名开发人员将 CORS 配置为验证“Origin header”URL，而白名单中的域名只是“requester.com”。现在，当攻击者创建如下请求时。

*GET /api/userinfo.php
主机:example.com
连接:关闭
产地:attackerrequester.com*

谦逊的服务器会回应

*HTTP/1.0 200 OK
访问控制允许来源:attackerrequester.com
访问控制允许凭证:真*

发生这种情况是因为后端的验证不佳，它只是检查是否存在“requester.com”。

就这些，非常感谢阅读:)