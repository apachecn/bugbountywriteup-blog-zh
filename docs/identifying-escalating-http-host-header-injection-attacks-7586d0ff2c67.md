# 识别和升级 HTTP 主机报头注入攻击

> 原文：<https://infosecwriteups.com/identifying-escalating-http-host-header-injection-attacks-7586d0ff2c67?source=collection_archive---------0----------------------->

HTTP 主机头的目的是帮助识别客户机想要与哪个后端组件通信。一些错误的配置和有缺陷的业务逻辑会使网站暴露于通过 *HTTP 主机头*的各种攻击。在深入研究之前，让我们先了解一些基本术语。

![](img/8fccaea9e212fb8a4b2bc54d18be4224.png)

## **什么是 HTTP 头？**

**HTTP 头**让客户机和服务器通过 HTTP 请求或响应传递附加信息。HTTP 头由不区分大小写的*名称、冒号(`:`)和值组成。*

## ***什么是主机头？***

**主机*请求头是强制头(根据 HTTP/1.1 ),它指定请求要发送到的服务器的主机和端口号。*

*如果没有包含端口，则默认端口是所请求服务的默认端口， *443 表示 HTTPS URL，80 表示 HTTP URL。**

*示例:`Host: mysite.net`*

## *什么是转发头？*

*`Forwarded`报头包含来自反向代理服务器的信息，当请求路径中涉及代理时，这些信息会被更改或丢失。*

*该割台的替代和实际标准版本为`X-Forwarded-For`、`X-Forwarded-Host`和`X-Forwarded-Proto headers`。*

*这个头用于调试、统计和生成位置相关的内容，并且通过设计，它暴露了隐私敏感信息，例如客户端的 IP 地址。*

*示例:`X-Forwarded-For: yoursafesite.net`*

# *什么是主机头攻击？*

**HTTP 主机头*攻击利用易受攻击的网站，这些网站以不安全的方式处理主机头的值。如果服务器隐式信任主机头，并且未能正确验证或转义它，则攻击者可能能够使用此输入来注入有害的有效负载，从而操纵服务器端的行为。*

## *到底是什么缺陷，哪里可能出错？*

*以前，每个 IP 地址只能承载一个域的内容。但今天，在同一个 IP 地址下访问多个网站和应用程序是很常见的。因为多个应用可通过相同的 IP 地址访问，关于通过代理虚拟托管或路由流量。寻找原点很容易迷路。因此，应用程序依赖于**主机**头来指定预期的接收者。*

# *如何测试应用程序是否容易受到主机头注入的攻击？*

*对主机头注入的测试很简单，您需要做的就是确定您是否能够修改 ***主机*** 头，并且仍然能够达到目标应用程序的请求。如果是这样，检查应用程序并观察这对响应有什么影响。*

*下图描述了来自 web 应用程序的有效请求-响应。*

*![](img/2d8e16eb708fc1359e974dcdd0714f9d.png)*

*主持人:demo.testfire.net*

***1。提供一个任意的主机头-** 尝试在请求中提供一个随机的主机，并观察应用程序的行为。如果收到 200 OK，攻击可能会进一步升级。*

*![](img/62470b2e6bc4cbb0dea4a5a016d8dc18.png)*

*主持人:attackersite.net——200 好吧*

***2。注入重复的主机头-** 尝试注入多个*主机*头，如果收到一个 200 OK，你可以把它当作一个肯定。但是在这里，在这种情况下，应用程序不能识别多个主机头。因此，会收到 400 状态代码。*

*![](img/ed1e5c04e5df1315ce84119e703e83ae.png)*

*多个主机标头*

***3。添加换行-** 尝试在*主机*标题中添加带有“attackersite”的换行。寻找一个成功的 200 OK 响应。*

*![](img/79f8846eb77398a3aad18bc7d08e4bc0.png)*

*换行— 200 可以*

***4。注入主机覆盖头-** 尝试注入主机覆盖头，如-`X-Forwarded-Host, X-Host, X-Forwarded-Server, X-HTTP-Host-Override
Forwarded`；通过一个成功的 200 OK 响应来查看报头是否被支持。*

*![](img/22321804c1a61f0aa4aff028372bb14f.png)*

*x-转发-主机:attackersite.com-200 OK*

***5。提供绝对 URL-** 尝试在主机头中的请求& attackersite 中提供绝对 URL。关注一个成功的 200 OK。*

*![](img/77bc37e198b528a3ebd62b63b01dfea6.png)*

*绝对 URL — 200 可以*

# *作为攻击者你能做什么？*

*如果不对头值进行适当的验证，攻击者可以提供无效的输入，使 web 服务器:*

1.  *将请求分派给列表中的第一个虚拟主机*
2.  *导致重定向到攻击者控制的域*
3.  *执行 web 缓存中毒*
4.  *操作密码重置功能*
5.  *操纵特定功能中的业务逻辑缺陷*
6.  *执行基于路由的 SSRF*
7.  *披露经典的服务器端漏洞，如 SQL 注入*

*![](img/310ebc7bd20f8447c8e6842e878ad2b4.png)*

# *升级攻击—如何利用配置错误的主机头？*

*HTTP 主机报头漏洞通常是由于**错误的假设“报头不是用户可控制的”而产生的。**这在主机头中创建了某种信任，并导致不正确的验证或用户输入的逃逸。*

## *执行 web 缓存中毒*

*看看*主机*头是否反映在没有 HTML 编码的*响应标记*中，或者甚至直接用于脚本导入。*

*`GET / HTTP/1.1
Host: attackersite.com`

当受害者访问易受攻击的应用程序时，web 缓存会提供以下内容。
`<link src=”http://attackersite.com/link" />`*

## *导致重定向到攻击者控制的域*

*尝试使用上述方法:将应用程序重定向到攻击者控制的域。查看攻击者的域中是否泄露或记录了任何敏感的令牌或密钥。*

## *操作密码重置功能*

*当创建使用*生成的秘密令牌*的密码重置链接时，如果密码重置功能包括*主机*报头值。*

*如果应用程序处理一个*攻击者控制的域*来创建一个密码重置链接，受害者可以**点击电子邮件中的链接**和**允许攻击者获得重置令牌**，从而重置受害者的密码。*

> *…电子邮件…*
> 
> *点击以下链接重置您的密码:`http://www.attackersite.com/account.php?module=login&action=resetpassword&token=<YOUR_SECRET_TOKEN>`*
> 
> *…电子邮件…*

## *导致重定向到受限内部站点— SSRF*

*易受攻击的主机标头也可能导致 SSRFs，请注意是否可以通过重定向访问内部受限站点。*

*[](https://medium.com/bugbountywriteup/server-side-request-forgery-ssrf-exploitation-technique-9bc4b4045fbd) [## 服务器端请求伪造— SSRF:利用技术

### 服务器端请求伪造，或 SSRF，是一个漏洞，允许攻击者使用易受攻击的服务器进行…

medium.com](https://medium.com/bugbountywriteup/server-side-request-forgery-ssrf-exploitation-technique-9bc4b4045fbd) 

&记住是否:

![](img/b0cd6e505eab7fd3587aad620c8caa95.png)

再见。

**参考文献:**

[](https://portswigger.net/web-security/host-header) [## HTTP 主机标头攻击|网络安全学院

### 在这一节中，我们将讨论错误的配置和有缺陷的业务逻辑如何将网站暴露给各种…

portswigger.net](https://portswigger.net/web-security/host-header) [](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_Host_Header_Injection) [## WSTG -最新

### 一个 web 服务器通常在同一个 IP 地址上托管几个 web 应用程序，通过…

owasp.org](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/17-Testing_for_Host_Header_Injection)*