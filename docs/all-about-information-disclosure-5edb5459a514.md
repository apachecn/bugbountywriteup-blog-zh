# 关于信息披露的一切

> 原文：<https://infosecwriteups.com/all-about-information-disclosure-5edb5459a514?source=collection_archive---------0----------------------->

![](img/5c1d6fe681aa780e4d156ceb73d97972.png)

你好黑客们，

希望你们做得很好，并狩猎大量的昆虫和美元！

好吧，让我们开始学习信息披露和寻找它们的方法。

> **什么是信息披露？**

信息泄露是指 web 应用程序未能妥善保护机密信息，导致向任何第三方泄露用户的敏感信息或数据或与用户相关的任何东西。

> **It 揭秘信息喜欢:-**

*   关于用户的数据，如用户名、信用卡信息或 web 应用程序列出的一些个人详细信息。
*   关于 web 应用程序及其后端数据的技术细节。
*   一些商业数据。

**信息泄露并不总是能被直接利用，因为有时他们允许黑客收集信息用于进一步的攻击，但这并不意味着你不能利用它。这总是取决于网络应用程序揭示了什么类型的信息，有时它会产生严重的影响。**

> **为什么会发生信息泄露？**

*   错误的配置
*   使用设计不良的应用程序
*   无法从公共内容中删除敏感内容

> **不同类型的信息披露问题:-**

**抢横幅**

标题抓取是一个收集信息的过程，如操作系统、服务器详细信息、运行的服务名称及其版本号，以及许多相关信息。

[https://hackerone.com/reports/460556](https://hackerone.com/reports/460556)

**源代码公开**

当 web 应用程序的后端环境的代码向公众公开时，就会出现这种情况。如果源代码文件被泄露，那么攻击者可能会使用这些信息来发现逻辑缺陷。这是高度可影响的。

[https://hackerone.com/reports/211418](https://hackerone.com/reports/211418)

**文件名和文件路径泄露**

这可能是由于对用户输入的不正确处理、后端的异常或 web 服务器的不适当配置造成的。有时，可以在 web 应用程序的响应、错误页面、调试信息等中找到或识别此类信息。

[https://hackerone.com/reports/979110](https://hackerone.com/reports/979110)

**对敏感数据的不当处理**

当敏感数据没有从源代码或其他地方删除时，就会发生这种情况。一些数据，如用户名、密码或一些重要的注释，可能会暴露一些敏感数据。还有很多其他可能性。

> **信息披露的常见来源:-**

*   错误消息
*   调试消息
*   备份文件
*   HTML 源代码中的开发人员注释
*   服务器和数据库消息
*   使用公共信息

> **寻找信息披露的可能途径:-**

*   使用 google Dorking、shodan 和 GitHub Dorking 查找公共信息。
*   总是仔细查看 HTML 源代码、调试消息和错误消息。
*   进行内容发现，并尝试通过各种绕过技术访问它。

> **如何防止信息泄露攻击** :-

*   将 web 服务器配置为不允许目录列表，并确保 web 应用程序始终显示默认网页。
*   不需要放在 web 服务器上的敏感数据、文件和任何其他信息都不应上传到 web 服务器上。
*   尽可能使用通用错误消息。不要给攻击者提供不必要的应用程序行为线索。
*   确保在服务器开放端口上运行的所有服务不会泄露其内部版本和版本信息。
*   不要在代码中硬编码凭证、API 密钥、IP 地址或任何其他敏感信息，包括名字和姓氏，即使是以注释的形式。

 [## 信息泄露漏洞

### 阿帕奇 Tomcat 信息披露 CVE-2017-7674

www.acunetix.com](https://www.acunetix.com/vulnerabilities/web/tag/information-disclosure/) [](https://portswigger.net/web-security/all-labs#information-disclosure) [## 所有实验室|网络安全学院

### SQL 注入跨站脚本跨站请求伪造(CSRF)点击劫持基于 DOM 的漏洞…

portswigger.net](https://portswigger.net/web-security/all-labs#information-disclosure) 

希望这对你们有用

黑客快乐！

推特句柄:-[https://twitter.com/Xch_eater](https://twitter.com/Xch_eater)