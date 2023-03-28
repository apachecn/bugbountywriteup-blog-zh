# 初学者的 BUG 搜索方法

> 原文：<https://infosecwriteups.com/bug-hunting-methodology-for-beginners-20b56f5e7d19?source=collection_archive---------1----------------------->

![](img/2b37f181043705b2af67597f80e45563.png)

在这篇文章中，我将描述我从初级水平开始寻找 bug 的过程。这篇文章纯粹是为 bug bounty 社区的新成员写的。我希望这能帮助你理解一个研究人员或 bug 猎人如何在 Web 应用程序中发现 bug。

**让我们从 Bug Bounty 的介绍开始:**

bug bounty 计划是由许多网站、组织和软件开发商提供的一项交易，通过该交易，个人可以因报告 bug(尤其是与安全利用和漏洞有关的 bug)而获得认可和补偿。

**注意:在这里我添加了一些工具和有用的链接，这些是我在寻找漏洞时使用的。**

这些是我每天用来寻找 bug 的工具和提示。

**有用的 YouTube 学习频道**

*   [**LiveOverflow**](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w)
*   [bug crowd](https://www.youtube.com/channel/UCo1NHk_bgbAbDBc4JinrXww)
*   **jack tutorials**
*   [**纳哈姆塞**](https://www.youtube.com/channel/UCCZDt7MuC3Hzs6IH4xODLBw)
*   [**STK**](https://www.youtube.com/channel/UCQN2DsjnYH60SFBIA6IkNwg)
*   [**security diots**](https://www.youtube.com/channel/UCPPAYs04kwfXcHnerm_ueFw)
*   **狩猎前必备技能:**

Linux 基础，网络基础，编程(编码时需要)

[*关于 HTTP 协议的基本思想及其报头(请求和响应)*](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)

Burpsuite、Metasploit、SqlMap、Nmap 等。

![](img/95f9a4ece0537dd376dae46f18e437ea.png)

*   如何选择我们的目标？

Bug 赏金平台

1.  **bug crowd** [https://www.bugcrowd.com/](https://www.bugcrowd.com/)
2.  **钢骨** [https://www.hackerone.com/](https://www.hackerone.com/)
3.  **塞纳克**
    [https://www.synack.com/](https://www.synack.com/)
4.  **日本 Bug 赏金计划**
    [https://bugbounty.jp/](https://bugbounty.jp/)
5.  **钴**
    https://cobalt.io/
6.  **零直升机** [https://zerocopter.com/](https://zerocopter.com/)
7.  **黑客证明** [https://hackenproof.com/](https://hackenproof.com/)
8.  **bounty factory**
    [https://bounty factory . io](https://bountyfactory.io/)
9.  **Bug 赏金程序列表【https://www.bugcrowd.com/bug-bounty-list/】** 
10.  **反黑客**
    [https://www.antihack.me/](https://www.antihack.me/)

或者我们可以通过搜索网站的**责任披露政策**从谷歌上找到目标。我建议从负责任的披露开始，这样就有更多的机会让报告被接受。然后在一次体验后开始用 Bug Bounty 平台。

*   **我们有了目标，那么如何开始呢？？**

如果你已经选择了你的目标，那么你应该开始寻找目标的子域。

或者，我们可以从我们可以从 ASN 获得的目标的 IP 块开始(下面提到了一些网站)

*   为什么我们需要子域？

![](img/9ff002b9e6b661d668a868753ddc2302.png)

因为示波器有助于识别弱目标

有时瞄准主域是不可能找到会让新手沮丧的 bug 的。因为高层或其他研究人员已经发现并向目标报告了漏洞。对于新手应该从其他子域开始。(诚然，大多数常见的漏洞已经被研究人员报告了，所以请记住，我们必须找到一个独特的目标和独特的错误。)

*   **如何找到子域？**

根据我的侦察，我使用以下工具来寻找目标的子域。

*   [子查找器](https://github.com/subfinder/subfinder)
*   [积聚](https://github.com/caffix/amass)
*   [子列表 3r](https://github.com/aboul3la/Sublist3r)
*   [Aquatone](https://github.com/michenriksen/aquatone)
*   [颠簸](https://github.com/guelfoweb/knock)

我们也可以通过在线侦察工具找到子域。(网站如下所示)

*   Virustotal(在工具中使用其 API)
*   Dnsdumpster
*   查找子域
*   pentest-工具
*   黑客目标
*   **子域接管漏洞:**

转到此链接，了解一些基础知识，以推进子域接管漏洞的概念。

https://github.com/EdOverflow/can-i-take-over-xyz

*   **使用 ASN (IP 块)发现目标:**

    [https://whois.arin.net/ui/query.do](https://whois.arin.net/ui/query.do)
*   **使用 Shodan 发现目标**

[https://www.shodan.io/search?query=org%3A%22Tesla+Motors%22](https://www.shodan.io/search?query=org%3A%22Tesla+Motors%22)

*   **品牌/ TLD 发现:**

这将通过搜索目标的获取来扩大目标范围

采集->[](https://www.crunchbase.com/search/acquisitions)**、维基百科**

**链接发现— ->打嗝蜘蛛网**

**加权和反向跟踪器→ domlink，内置**

*   ****谷歌中的商标:**”“特斯拉 2018”“特斯拉 2019”“特斯拉 2020”**inurl:**特斯拉**
*   ****子查找器****
*   ****捉鬼敢死队****
*   ****Aquatone****
*   ****子域编号:****

**在这里你可以找到原来的脚本[https://github.com/appsecco/bugcrowd-levelup-](https://github.com/appsecco/bugcrowd-levelup-)子域名[枚举](https://github.com/appsecco/bugcrowd-levelup-subdomain-enumeration)**

****注意:请替换脚本中使用的 API 密钥，这可能是无效的，会导致子域数量减少(我建议使用 virustotal API 密钥)****

*   ****演示文稿:****

**幻灯片可从以下网址获得:[https://speaker deck . com/yamakira/esoteric-su B- domain-enumeration-techniques](https://speakerdeck.com/yamakira/esoteric-sub-domain-enumeration-techniques)**

*   ****子域枚举与 SPF 记录****
*   ****使用 CSP****
*   ****DNSrecon****
*   ****替代域名****
*   ****利用挖掘进行区域转移****
*   ****DNSSEC****
*   ****区域行走 NSEC — LDNS****
*   ****端口扫描:****

**端口扫描对于发现运行在非标准或标准端口上的目标非常重要。**

**端口扫描我用过 **NMAP** 和 **Masscan** 和 **Aquatone scan** 。**

**一些研究者一旦发现子域运行在标准或非标准端口上，就开始检查子域接管漏洞。**

*   ****枚举目标(端口扫描)****
*   ****NMAP****
*   ****视觉识别****

**这一部分将帮助我们找到在目标机器的标准或非标准端口上运行的应用程序。**

**如果在特定端口上运行的目标机器上发现以下工具，则这些工具正在抓取横幅。这将有助于我们分类列出我们的目标子域。**

*   ****目击者****
*   ****Wayback 枚举→ > waybackurl****

**如果我们看到任何一个 HTTP 响应，如 401、403、404，这项技术将会帮助我们。这将显示您使用存档旧的存储数据。**

**在这里，我们可以找到一些敏感信息，即使目标页面目前无法访问。
[**https://archieve.org/web**](https://archieve.org/web)**

*   ****waybackurls****
*   ****解析 JavaScript****

**解析 JS 对于找到目标使用的目录非常有用。我们可以使用这些类型的工具，而不是在目标上强制目录列表**

****注:**蛮干目录也是好事。总是使用多种技术从目标中找到目录**(我用目录破坏者&打嗝入侵者】找到了 Hotsar Aws 凭证)****

*   ****链接查找器****
*   ****目录搜索****
*   ****Dirb****
*   ****内容发现:《捉鬼敢死队》****
*   ****凭据 brute force:“BrutesprayBrutespray”****

**这些工具能够暴力破解不同类型的协议，如 http、ssh、smtp 等**

*   ****技术识别和漏洞发现:****

**这里我在浏览器上使用了 **Wappalyzer** 和 **build 和**插件。 [**Whatweb**](https://tools.kali.org/web-applications/whatweb) 工具也是我用来查找他们在目标上使用了哪些技术的。**

**以下工具用于查找目标上的技术和基于技术的漏洞。**

*   ****WPScan****
*   ****Cmsmap****
*   **在开始测试之前，我向 bug 猎人推荐这本书，因为它对理解利用 bug 很有帮助。**

**测试是基于我们的意见。其中一些是从 xss 和其他漏洞开始的，我们可以很容易地从目标中找到这些漏洞。**

**你仍然被测试 bug 困扰，这意味着你可以开始阅读以下书籍，它们对 Bug 猎人或应用程序渗透测试者总是有帮助的。**

1.  **[https://www . Amazon . in/we B- Application-Hackers-Handbook-exploining/DP/8126533404](https://www.amazon.in/Web-Application-Hackers-Handbook-Exploiting/dp/8126533404)**
2.  **[https://www . OWASP . org/index . PHP/OWASP _ Testing _ Guide _ v4 _ Table _ of _ Contents](https://www.owasp.org/index.php/OWASP_Testing_Guide_v4_Table_of_Contents)**
3.  **【https://leanpub.com/web-hacking-101 **

****对于我们的手机黑客朋友:****

1.  **移动应用黑客手册**
2.  **[iOS 应用安全](http://amzn.to/2d9yo7m)**
3.  **Owasp 移动应用程序安全**

**我希望这些书对如何测试 bug 很有帮助**

****备忘单****

*   **[SQL 注入备忘单](http://pentestmonkey.net/blog/mssql-sql-injection-cheat-sheet/)**
*   **[XSS 小抄](https://gist.github.com/kurobeats/9a613c9ab68914312cbb415134795b45)**
*   **[XXE 有效载荷](https://gist.github.com/staaldraad/01415b990939494879b4)**

****笔测试方法****

1.  **[渗透测试框架](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)**
2.  **[渗透检测执行标准](http://www.pentest-standard.org/index.php/Main_Page)**
3.  **[WASC 威胁分类](http://projects.webappsec.org/w/page/13246978/Threat-Classification)**
4.  **[OWASP 十大项目](http://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)**
5.  **[社会工程框架](http://www.social-engineer.org/framework/general-discussion/)**

****流行的谷歌呆子使用(寻找 Bug 赏金网站)****

**![](img/edb0f61c7f95dc212879158ff04758e7.png)**

**[米切尔·罗](https://unsplash.com/@mitchel3uo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片**

1.  ****地点:。欧盟责任披露****
2.  ****inurl:index.php？id=****
3.  ****网站:。nl bug 赏金****
4.  ****【索引】inurl:wp-content/(识别 Wordpress 网站)****
5.  ****inurl:" q =用户/密码"(用于查找 drupal cms )****

****浏览器插件****

*   **chrome:[http://resources . infosecinstitute . com/19-extensions-to-turn-Google-chrome-into-penetration-testing-tool/](http://resources.infosecinstitute.com/19-extensions-to-turn-google-chrome-into-penetration-testing-tool/)**
*   **Firefox:[http://resources . infosecinstitute . com/use-Firefox-browser-as-a-a-penetration-testing-tool-with these-add-ons/](http://resources.infosecinstitute.com/use-firefox-browser-as-a-penetration-testing-tool-with-these-add-ons/)**
*   ****我的提示&招数****
*   **臭虫赏金猎人提示#1- **总是阅读源代码****
*   **臭虫赏金猎人小贴士#2- **尝试猎杀子域****
*   **Bug 赏金猎人提示#3- **总是检查后端 CMS &后端语言****
*   **Bug 赏金狩猎技巧#4- **谷歌呆子很有帮助****
*   **Bug 赏金狩猎技巧#5- **活跃思维——跳出思维定势；)****

**![](img/1ddd604aa14a61b5ae8dfdc228efc78d.png)**

**权力越大，责任越大**

****“特别感谢 Jhaddix 与我们分享这一方法论”****

**推特:[https://twitter.com/Mah3Sec](https://twitter.com/Mah3Sec_)**