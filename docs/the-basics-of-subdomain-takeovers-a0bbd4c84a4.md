# 子域接管的基础

> 原文：<https://infosecwriteups.com/the-basics-of-subdomain-takeovers-a0bbd4c84a4?source=collection_archive---------0----------------------->

![](img/7f5e44964b49cc5e899429ac2cd27209.png)

子域接管是一个漏洞，它允许攻击者提供不属于该攻击者的子域的内容。使子域接管成为可能的最常见情况是:

1)受影响子域的 CNAME 记录指向可能被攻击者声称的域

2)A 记录指向一个可以被攻击者注册的 IP 地址

特别是第一种情况很常见，尤其是当您的子域指向云提供商(如 Amazon AWS)的主机名时。所以让我们假设你在亚马逊的 S3 网站桶上运行一个博客，你已经给这个桶分配了主机名*myblog.amazonaws.com*。由于您希望这个博客在您自己的域名下是可访问的，所以您创建了 blog.example.com 的域名*和一个从该域名指向 myblog.amazonaws.com 的*的指针(通过 CNAME 记录)。现在，如果有人在浏览器中打开你的域名*blog.example.com*，浏览器知道它应该加载来自*myblog.amazonaws.com*的内容。**

稍后，您可能会决定关闭博客，并删除 S3 桶及其主机名*myblog.amazonaws.com*。因此，Amazon 域再次变得可用，其他人可以注册该域，并为此主机名创建一个新的 S3 存储桶。现在，一个问题可能会出现。如果你忘记删除从*blog.example.com*到*myblog.amazonaws.com 的指针(即 CNAME 记录)，那么*新的 S3 桶(现在已经不属于你了)的内容将会发送给访问你的域名*blog.example.com*的每个人。

攻击者可以利用这一点，通过您的子域发送他/她的 S3 桶中的恶意内容。

# 子域接管的风险

各种风险可能是子域接管的结果。

**绕过 CSRF 保护**

可能你已经决定信任你所有的子域，因此你只依靠同一个站点的 cookies 作为 CSRF 保护。由于 SameSite 仅保护您的 web 应用程序免受跨站点请求的 CSRF 攻击，攻击者可能会使用子域接管来通过同一站点的受损子域绕过 CSRF 保护。

如果您设置了 cookie，并且您将域属性设置为您的主域，例如*example.com*，则访问和修改 cookie 所有子域也会收到此 cookie。因此，攻击者可以使用受威胁的子域来访问所有这些 cookies。此外，当受害者通过指定域属性访问受损域时，攻击者可以在受害者浏览器中为其他子域设置 cookies。

**网络钓鱼**

由于受危害的子域提供的内容来自受信任的(子)域，攻击者可以发起网络钓鱼攻击，窃取使用您的站点的受害者的凭据。

**OAuth 2.0 令牌泄露**

OAuth 2.0 是一种协议，它允许用户授权第三方应用程序访问用户的受保护资源，而无需向该应用程序提供用户的凭证。相反，用户在受信任的站点进行身份验证，然后用户被重定向到应用程序。重定向包含一个可用于访问资源的令牌(实际上，令牌被转换为访问令牌，但这与此无关)。

仅当目标 URL 可信时，才会执行重定向。如果所有子域都被信任为目标 URL，则攻击者可能能够通过指向受损子域的目标 URL 获得 OAuth 2.0 令牌。

**绕过内容安全策略**

内容安全策略是一个 HTTP 响应头，您可以在其中指定允许加载 JavaScript 等内容的位置。这有助于减轻跨站点脚本(XSS)攻击。

如果您已经将所有子域列入白名单，那么只需一个受损子域就足以绕过内容安全策略提供的保护。

# 一、AAAA 和 CNAME 的记录

正如已经提到的原因，它可能会来到一个子域接管漏洞是最常见的错误配置 CNAME 或 A 记录。随着 IPv6 的日益普及，AAAA 记录也可能是一个原因。我们来看看为什么会这样。

当您注册一个域时，您可以在域名系统(DNS)中以记录的形式存储该域及其子域的各种信息。例如，一个域的 IPv4 地址存储在 A 记录中。浏览器使用这些记录将人类可读的主机名解析为机器可用的 IPv4 地址。与 A 记录非常相似，AAAA 记录(也称为“quad-A”)存储 IPv6 地址。之所以使用 AAAA 这个名称，是因为 IPv6 地址占用 16 个字节(128 位)，是 IPv4 地址的四倍，而 IP v4 地址只占用 4 个字节(32 位)。

另一种用于搜索子域接管漏洞的 DNS 记录类型是 CNAME 记录(“规范名称记录”)。该记录用于将一个域名映射到另一个域名。映射到第二个域名的域名通常被称为第二个域的“别名”，因为它是一个域的附加名称。

一个域的记录可能如下例所示:

```
NAME               TYPE
-------------------------------------------
bar.example.com    CNAME    foo.example.com
foo.example.com    A        192.126.0.10
```

如果你在浏览器中输入 bar.example.com*的地址*，浏览器首先试图获取这个地址的 A 记录。由于该地址只有一条指向*foo.example.com*的 CNAME 记录，浏览器将查找该域名的 A 记录。现在，这个域名有一个 A 记录，浏览器打开 IP 地址 192.168.0.10。

也有可能给两个主机名相同的 A 记录。然而，使用 foo.example.com 的 CNAME 记录的好处是，如果 IP 地址改变，你必须更新它。

存在各种其他的 DNS 记录类型，你可以在维基百科上找到一个列表。然而，搜索子域接管漏洞的 CNAME 和一个域的记录是最有趣的一个。

# 用 dig 在 Linux 命令行上获取 A 和 CNAME 记录

当你使用 Linux 时，很容易得到一个域的 A 和 CNAME 记录，这是大多数 Linux 发行版默认安装的工具。您可以使用的一个流行的 DNS 查找工具是“dig”。

要查找 www.cnn.com[域名](http://www.cnn.com)的 DNS 记录，您可以如下执行 dig:

```
dig [www.cnn.com](http://www.cnn.com)
```

输出可能如下所示:

```
; <<>> DiG 9.16.1-Ubuntu <<>> www.cnn.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 40442
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;www.cnn.com.                   IN  A;; ANSWER SECTION:
www.cnn.com.              23    IN  CNAME  turner-tls.map.fastly.net.
turner-tls.map.fastly.net. 11	IN	A      151.101.13.67
```

有趣的内容位于以行开头的答案部分”；；回答部分:“。在本例中，查询的答案是:

```
www.cnn.com.              23    IN  CNAME  turner-tls.map.fastly.net.
turner-tls.map.fastly.net. 11	IN	A      151.101.13.67
```

我们可以看到 www.cnn.com 的[只是 turner-tls.map.fastly.net 域名的别名，因为它只有一个指向这个域名的 CNAME 记录。在第二行中，我们可以看到 A 记录，它包含该域的 IPv4 地址。](https://www.cnn.com/)

**警告！**dig 的输出可能取决于您使用的名称服务器，因为名称服务器的行为可能会有所不同。例如，有时当被查询的域名只有 CNAME 记录时，答案部分可能是空的。在这种情况下，您应该尝试明确地查询 CNAME 记录。

# 使用摘要时指定记录类型

要指定记录类型，您只需要添加记录类型的名称作为附加参数。例如，要仅查询 CNAME 记录，只需添加“cname”作为附加参数:

```
dig www.cnn.com cname
```

现在，输出中的答案部分可能如下所示:

```
;; ANSWER SECTION:
www.cnn.com.    5    IN   CNAME    turner-tls.map.fastly.net.
```

在这个答案中，只显示了 CNAME 的记录。

除了“cname ”,您还可以使用“a”表示 a 记录，“aaaa”表示 aaaa 记录，“mx”表示 mx 记录，依此类推。如果您的名称服务器支持“任何”，将返回一个域的所有 DNS 记录。

# 使用 dig 时指定名称服务器

如果您的名称服务器不支持“any”，您可以尝试使用另一个名称服务器。例如，您可以指定 Google 域名服务器 8.8.8.8 的地址，方法是将该地址作为一个附加参数添加进来，并在地址前面加上一个@符号。这里有一个例子:

```
dig @8.8.8.8 www.cnn.com any
```

例如，当在我的 ISP 的名称服务器上查询域名[gmx.net](http://gmx.net/)的“any”时，我得到以下答案:

```
; <<>> DiG 9.16.1-Ubuntu <<>> gmx.net any
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: SERVFAIL, id: 44888
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;gmx.net.			IN	ANY
```

如您所见，该回复不包含回答部分。相反，响应的状态是“SERVFAIL”(第四行)。但是，如果我使用名称服务器 8.8.8.8，我会得到以下(简短的)响应:

```
dig @8.8.8.8 gmx.net any; <<>> DiG 9.16.1-Ubuntu <<>> @8.8.8.8 gmx.net any
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 61609
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 27, AUTHORITY: 0, ADDITIONAL: 1;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;gmx.net.               IN  ANY;; ANSWER SECTION:
gmx.net.		21478	IN	A	82.165.229.87
gmx.net.		21478	IN	RRSIG	A 8 2 86400 20220511065307 [...]
gmx.net.		21478	IN	NS	ns-gmx.ui-dns.de.
gmx.net.		21478	IN	NS	ns-gmx.ui-dns.biz.
gmx.net.		21478	IN	NS	ns-gmx.ui-dns.com.
gmx.net.		21478	IN	NS	ns-gmx.ui-dns.org.
gmx.net.		21478	IN	RRSIG	NS 8 2 86400 20220511065307 [...]
gmx.net.		21478	IN	SOA	ns-gmx.ui-dns.org. dnsadmin.1und1.de. [...]
[...]
```

请注意:即使状态为 NOERROR，对于不同的域名服务器,“any”的响应也可能不同。这是我从我的 ISP 的域名服务器上得到的结果，如果我为 google.com 查询“任何”。

```
dig google.com any; <<>> DiG 9.16.1-Ubuntu <<>> google.com any
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51127
;; flags: qr rd ra; QUERY: 1, ANSWER: 8, AUTHORITY: 0, ADDITIONAL: 1
[...];; ANSWER SECTION:
google.com.		203	IN	A	142.250.185.206
google.com.		113	IN	AAAA	2a00:1450:4001:82b::200e
google.com.		136	IN	MX	10 smtp.google.com.
google.com.		48	IN	SOA	ns1.google.com. dns-admin.google.com. [...]
google.com.		63471	IN	NS	ns4.google.com.
google.com.		63471	IN	NS	ns1.google.com.
google.com.		63471	IN	NS	ns2.google.com.
google.com.		63471	IN	NS	ns3.google.com.
```

这是我使用名称服务器 8.8.8.8 得到的结果:

```
dig @8.8.8.8 google.com any; <<>> DiG 9.16.1-Ubuntu <<>> @8.8.8.8 google.com any
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 10356
;; flags: qr rd ra; QUERY: 1, ANSWER: 18, AUTHORITY: 0, ADDITIONAL: 1;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.			IN	ANY;; ANSWER SECTION:
google.com.		265   IN	A	142.250.185.206
google.com.		265   IN	AAAA	2a00:1450:4001:812::200e
google.com.		3565  IN	TXT	"docusign=1b0a6754-49b1-4db5-8540-d2c12664b289"
google.com.		3565  IN	TXT	"google-site-verification=TV9-DBe4R80X4v0M4U_bd_J9cpOJM0nikft0jAgjmsQ"
google.com.		25    IN	SOA	ns1.google.com. dns-admin.google.com. 444806637 900 900 1800 60
google.com.		3565  IN	TXT	"v=spf1 include:_spf.google.com ~all"
google.com.		3565  IN	TXT	"docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
google.com.		3565  IN	TXT	"MS=E4A68B9AB2BB9670BCE15412F62916164C0B20BB"
google.com.		265   IN	MX	10 smtp.google.com.
google.com.		21565 IN	NS	ns1.google.com.
google.com.		3565  IN	TXT	"apple-domain-verification=30afIBcvSuDV2PLX"
google.com.		21565 IN	NS	ns3.google.com.
google.com.		3565  IN	TXT	"google-site-verification=wD8N7i1JTNTkezJ49swvWW48f8_9xveREV4oB-0Hf5o"
google.com.		21565 IN	NS	ns4.google.com.
google.com.		21565 IN	CAA	0 issue "pki.goog"
google.com.		3565  IN	TXT	"facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
google.com.		21565 IN	NS	ns2.google.com.
google.com.		3565  IN	TXT	"globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8=
```

但是，如果我显式地查询 txt 记录，我会从两个名称服务器得到相同的结果:

```
;; ANSWER SECTION:
google.com.		3600	IN	TXT	"globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8="
google.com.		3600	IN	TXT	"google-site-verification=TV9-DBe4R80X4v0M4U_bd_J9cpOJM0nikft0jAgjmsQ"
google.com.		3600	IN	TXT	"apple-domain-verification=30afIBcvSuDV2PLX"
google.com.		3600	IN	TXT	"docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
google.com.		3600	IN	TXT	"v=spf1 include:_spf.google.com ~all"
google.com.		3600	IN	TXT	"google-site-verification=wD8N7i1JTNTkezJ49swvWW48f8_9xveREV4oB-0Hf5o"
google.com.		3600	IN	TXT	"MS=E4A68B9AB2BB9670BCE15412F62916164C0B20BB"
google.com.		3600	IN	TXT	"docusign=1b0a6754-49b1-4db5-8540-d2c12664b289"
google.com.		3600	IN	TXT	"facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
```

因此，如您所见，任何查询都可能有问题，建议您明确查询您感兴趣的每条记录。另见[此处](https://serverfault.com/a/294370)和[此处](https://blog.cloudflare.com/rfc8482-saying-goodbye-to-any/)了解为什么任何查询都有问题的更多细节。

# NXDOMAIN

如果您在回答中得到 NXDOMAIN 状态，则无法使用 DNS 解析该域。NX 代表“不存在”。实际上，名称“不存在”可能有点误导，因为即使该域名存在，您也可能会获得此状态，例如，当该域名存在，但只有一个指向无法解析的域名的 CNAME 记录时。

从用户的角度来看，该域不存在或存在但只有一个指向无法解析的域的 CNAME 记录并没有什么区别。如果您在浏览器中输入这样的域，在两种情况下都会得到相同的结果。

然而，后者可能导致子域接管。因此，如果您在对 DNS 查询的响应中获得 NXDOMAIN as 状态，您应该验证该域没有受到可能的子域接管的影响。

# 使用 dig 检查可能的子域接管漏洞

有了所有这些信息，您应该能够验证一个域没有受到可能的子域接管的影响。这些步骤是:

1.  使用“挖掘 A”来检查该域的 A 记录。如果存在未分配的 IP，则该域可能容易被子域接管，因为攻击者可能能够注册该地址。
2.  使用“dig cname”来检查该域的 cname 记录。如果 CNAME 记录指向一个无法解析的域，攻击者就可能获得该域的所有权。例如，如果别名指向一个可以注册的域名，或者指向一个在亚马逊 AWS 上不存在的 S3 网站。然后，攻击者还可以创建一个 S3 存储桶，并重用该名称。