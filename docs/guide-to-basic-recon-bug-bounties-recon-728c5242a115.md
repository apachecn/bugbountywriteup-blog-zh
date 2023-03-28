# 如何在追逐昆虫赏金之前做好侦察工作

> 原文：<https://infosecwriteups.com/guide-to-basic-recon-bug-bounties-recon-728c5242a115?source=collection_archive---------1----------------------->

![](img/9bec53d09e7593754f590570269df27d.png)

***今天我写的是*** 昆虫赏金&侦察之间的爱情故事，但在此之前，我应该说我不是一个专家，这篇文章反映了我的个人观点。

这篇博文将关注侦察&在 Bug 赏金计划中去哪里寻找 Bug，这不是一个关于如何在技术意义上找到 Bug 的指南，而是一个你可以用来找到 Bug 的策略案例。

我假设你已经知道渗透测试，因此我不会解释如何测试漏洞，而是**在哪里**测试他们&你可以使用的工具。这主要只是一个概述，说明在开始审计之前，人们将如何绘制出目标地点，并有效地进行侦察，以获得尽可能多的有关该地点的信息。

侦察是任何渗透测试的基本要素。

## **竞争？**

![](img/39de888179c5221aa7d19daf41e14e98.png)

臭虫奖励计划并不简单，关于臭虫奖励计划你需要记住的是有很多竞争。当你参加一个 bug bounty 项目时，你不仅要和网站的安全性竞争，还要和成千上万参与这个项目的人竞争。因此，批判性思考很重要。

这就是为什么被动和主动侦察对赏金项目特别重要，因为你需要比常规渗透测试看得更深。

## 侦察在测试中的重要性？

![](img/6169c6ca8e10f6231d65ed7cebb10906.png)

在许多情况下，提取相关信息可以起到改变游戏规则的作用。提取这些信息非常简单，而且有些容易。有时，recon 可以超越收集基本信息以了解系统，还可以识别可能直接导致利用的信息，有时不需要实际接触被测试的实体。

即使在具有如此重要的意义之后，这一阶段也没有得到足够的重视，大多数测试直接关注于利用。这里的关键点是，剥削当然重要，但执行彻底的侦察可能会证明非常有帮助，也使它更容易，更快，更隐蔽。

## **确定目标？**

![](img/d9b955cb830f544ac3f3375e5c68b0f0.png)

理想情况下，你会想要选择一个范围广泛的项目。你也会想要寻找一个范围内有更多漏洞的赏金程序。赏金计划的攻击面越广，被认为有效的漏洞范围越广，获得有效支付的机会就越大。

在选择了你要尝试的赏金计划后，下一个基本步骤就是绘制出你的攻击面，并对其有更多的了解。

## **是时候绘制出目标了！**

![](img/9b78776013dc477257c868c8301fd4c0.png)

第一条规则(这是我最常忘记遵守的😛并最终完全打乱了这一天)，即正确阅读奖励条款并清楚了解哪些域在范围内以及哪些形式的漏洞被视为有效报告。

有时候，我忘记这样做了，然后倒楣的事情发生了，提交了不在赏金计划范围内的东西，告诉运行该计划的人，你没有正确阅读条款，这将导致他们不认真对待你未来的报告。我是认真的

因此，在进行任何攻击或测试之前，你真的需要主动/被动地对你的目标进行人员侦察，以有效地规划出你可以做的大多数事情，从而更多地了解你的目标！

> ***“亲近你的朋友，更亲近你的敌人？”***

因此，遵循这句话，我总是把目标放在我的心脏附近，并尽可能多地描绘出来😉它总是给我一个想法，所有的东西是如何被构造的&所有的东西是如何在目标上工作的。

我通过挖掘域名、电子邮件服务器和社交网络连接的信息来启动每个程序。范围越大，找到 bug 的几率就越高。假设所有子域都在作用域内，那么第一步就是枚举有效的子域。

为此我使用了不同的工具。

**榜单:**

*   [https://pentest-tools.com/](https://pentest-tools.com/)
*   [https://virustotal.com/](https://virustotal.com/)
*   [https://www.shodan.io/](https://www.shodan.io/)
*   [https://crt.sh/?q=%25taregt.com](https://crt.sh/?q=%25taregt.com)
*   [https://dnsdumpster.com/](https://dnsdumpster.com/)
*   [https://censys.io](https://censys.io)
*   [http://dnsgoodies.com](http://dnsgoodies.com)

这些是我每天使用的网🙂

*   [https://bitbucket.org/LaNMaSteR53/recon-ng](https://bitbucket.org/LaNMaSteR53/recon-ng)
*   [https://github.com/michenriksen/aquatone](https://github.com/michenriksen/aquatone)
*   [https://github.com/aboul3la/Sublist3r](https://github.com/aboul3la/Sublist3r)
*   [https://github.com/rbsec/dnscan](https://github.com/rbsec/dnscan)
*   [https://github.com/Cleveridge/cleveridge-subdomain-scanner](https://github.com/Cleveridge/cleveridge-subdomain-scanner)

现在，在得到所有的子域后，我们应该进行第二步，我认为是端口扫描。我们有两种老方法(但老是金 lol)是通过 nmap 扫描有限的端口，选择一个或可能 1-50000 天知道你要做什么😛

Masscan 也可以帮助 https://github.com/robertdavidgraham/masscan[的](https://github.com/robertdavidgraham/masscan)

我多次使用的第二种方法是使用 aquatone 扫描子域，然后使用它扫描端口，您可以选择扫描通用/大型/巨型等端口。

最好使用 aquatone，但理想情况下，您希望扫描与其子域相关联的每个 IP 地址，并将输出保存到文件中，然后查找运行在异常端口上的任何服务或运行在可能易受攻击的默认端口(FTP、SSH 等)上的任何服务。您还需要查找正在运行的服务的版本信息，以确定是否有任何内容已经过时或存在潜在漏洞。这需要时间，但也会带来结果😉

另外，不要局限于子域，尝试提取虚拟主机🙂工具如

*   [https://pentest-tools . com/information-gathering/find-virtual-hosts](https://pentest-tools.com/information-gathering/find-virtual-hosts)
*   [https://github.com/jobertabma/virtual-host-discovery](https://github.com/jobertabma/virtual-host-discovery)

滑行移动更快试试[https://github.com/ChrisTruncer/EyeWitness](https://github.com/ChrisTruncer/EyeWitness)🙂

或者也许

https://github.com/breenmachine/httpscreenshot/

你应该在侦察过程中做笔记，以避免混乱。你想怎么做就怎么做，但由于参与 bug bounty 项目主要涉及黑盒测试，所以了解网站的结构并绘制出地图以有效地发现 bug 是非常重要的。

这只是最基本的，你可能想看看标题，看看有哪些安全选项，例如寻找 X-XSS-保护:或 X-帧-选项:拒绝的存在。知道有什么安全措施就意味着你知道自己的局限性。也要注意 WAFs，我建议你可以使用 WafW00f

*   [https://github.com/sandrogauci/wafw00f](https://github.com/sandrogauci/wafw00f)(WAF w00 f 识别并采集 Web 应用防火墙(WAF)产品的指纹。)

另外，你也应该寻找任何信息披露和激光有时目录列表，或者也许目录扫描可以帮助其他东西，你可以使用 Dirbuster

*   [https://www . OWASP . org/index . PHP/Category:OWASP _ dir buster _ Project](https://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)

打嗝组曲，蜘蛛会是你最好的朋友。只要确保你的范围设置正确，这样你就不会浪费时间去搜索不需要的域名。此外，入侵者是完全必要的目录暴力。下载 https://github.com/danielmiessler/SecLists 的资源库，其中有大量列表，可以跨多个平台发现内容。如果你有打嗝套件专业版，我强烈推荐使用反射器扩展。这将向您显示在打嗝爬行时反映到响应中的任何参数。

使用 robots.txt 来确定可能包含有用信息的目录，查找禁止规则。

也蜘蛛主机的 API 端点😉还要做笔记 lol

wappalyzer 可以很好地用于检查 CMS🙂

在侦查期间提取 S3 桶是个不错的主意，手动寻找或者使用类似的工具。

*   [https://github.com/yasinS/sandcastle](https://github.com/yasinS/sandcastle)
*   [https://digi.ninja/projects/bucket_finder.php](https://digi.ninja/projects/bucket_finder.php)

基本上，当我做完这些事情后，我会记下子域名/IP 或域名。

我的阶梯式笔记通常包含:

*   Whois 信息
*   子域
*   目录信息
*   S3 水桶
*   社交账户
*   API 端点
*   电子邮件
*   虚拟主机
*   后端 IP 地址
*   开放端口/服务正在运行
*   服务版本信息(如果适用)
*   服务器横幅
*   目录列表
*   状态安全标题
*   晶片(+晶片类型)

在此之后，我开始创建和捕获所有类型的请求和响应、接受的用户输入(GET/POST/COOKIES)以及其他点。

也不要忘记你最好的朋友谷歌:p 使用谷歌呆子你可以自己做或使用别人做的😉

尝试一下

*   [https://pentest-tools . com/information-gathering/Google-hacking](https://pentest-tools.com/information-gathering/google-hacking)
*   [https://github.com/1N3/Goohak/](https://github.com/1N3/Goohak/)
*   [https://github.com/ZephrFish/GoogD0rker/](https://github.com/ZephrFish/GoogD0rker/)

想建造你自己的吗？看一看[https://support.google.com/websearch/answer/2466433?hl=en](https://support.google.com/websearch/answer/2466433?hl=en)

确保花尽可能多的时间进行侦察，直到你对网站的运作有一个很好的感觉，

甚至有时候被动侦察会导致一些重要信息泄露。例如，在 github 或 pastebin 中搜索公司名称，偶然发现某个粗心的开发人员编写的某个随机来源最终出现在网上。

为此我更喜欢

*   https://github.com/1N3/Sn1per[(网页版)](https://github.com/1N3/Sn1per)
*   https://github.com/michenriksen/gitrob(github 专用)
*   [https://github.com/dxa4481/truffleHog](https://github.com/dxa4481/truffleHog)
*   [https://github.com/IOActive/RepoSsessed](https://github.com/IOActive/RepoSsessed)
*   [https://github.com/anshumanbh/git-all-secrets](https://github.com/anshumanbh/git-all-secrets)

我用这些得到了一些好的报告🙂

不要忘记手动深入研究 Js 文件，你会喜欢它，但节省时间是目标，所以尝试使用工具，如

*   [https://github.com/jobertabma/relative-url-extractor](https://github.com/jobertabma/relative-url-extractor)

另外，最好的事情之一是寻找旧的内容，可以给你的网站结构或可能 vuln 端点的想法😉为了这种用途

*   [https://web.archive.org/](https://web.archive.org/)
*   [https://gist . github . com/mhmdiaa/2742 C5 e 147d 49 a 804 b 408 bfed 3d 32d 07](https://gist.github.com/mhmdiaa/2742c5e147d49a804b408bfed3d32d07)
*   [https://gist . github . com/mhmdiaa/ADF 6 BFF 70142 e 5091792841 D4 b 372050](https://gist.github.com/mhmdiaa/adf6bff70142e5091792841d4b372050)

也许 reversewhois 查找将有助于发现更多的潜在目标，但要确保它们在范围内

*   [http://viewdns.info/reversewhois/?q=](http://viewdns.info/reversewhois/?q=)

好的，然后有一个叫做 **PunkSpider** 的东西。([https://www.punkspider.org](https://www.punkspider.org))“它是一个**全球 web 应用漏洞搜索引擎**。但是不要太兴奋。

PunkSpider 玩起来很酷，但是不够深入。你也不能在搜索中使用通配符，这使得搜索多个子域变得很困难。但花几分钟四处看看也无妨。谁知道呢，也许你会走运？

嗯，这几乎是我在侦察期间所做的一切&在开始真正的昆虫赏金猎人之前！所以希望我没有错过任何我执行的基本侦察…但是帮助更多地看这些臭虫赏金参考。有时候，你很幸运地在不同的 bug 奖励计划中发现了相同的 Bug。

## Bug 赏金参考

一份按 bug 性质分类的 bug 赏金记录清单，作者: [***恩加朗克****这是受*](https://github.com/ngalongc)*[https://github.com/djadmin/awesome-bug-bounty](https://github.com/djadmin/awesome-bug-bounty)的启发*

*我的意图是制作一个公开披露的 bug bounty write 的常见漏洞的完整列表，并让 bug Bounty Hunter 使用此页面作为参考，当他们在 Bug 狩猎期间想要获得特定类型的漏洞的一些见解时，可以随时提交 pull 请求。好了，闲聊够了，我们开始吧。*

*   *[XSSI](https://github.com/ngalongc/bug-bounty-reference#xssi)*
*   *[跨站点脚本(XSS)](https://github.com/ngalongc/bug-bounty-reference#cross-site-scripting-xss)*
*   *[蛮力](https://github.com/ngalongc/bug-bounty-reference#brute-force)*
*   *[SQL 注入(SQLi)](https://github.com/ngalongc/bug-bounty-reference#sql-injection)*
*   *[外部 XML 实体攻击(XXE)](https://github.com/ngalongc/bug-bounty-reference#xxe)*
*   *[远程代码执行(RCE)](https://github.com/ngalongc/bug-bounty-reference#remote-code-execution)*
*   *[反序列化](https://github.com/ngalongc/bug-bounty-reference#deserialization)*
*   *[图像色板](https://github.com/ngalongc/bug-bounty-reference#image-tragick)*
*   *[跨站点请求伪造(CSRF)](https://github.com/ngalongc/bug-bounty-reference#csrf)*
*   *[不安全的直接对象引用(IDOR)](https://github.com/ngalongc/bug-bounty-reference#insecure-direct-object-reference-idor)*
*   *[窃取访问令牌](https://github.com/ngalongc/bug-bounty-reference#stealing-access-token)*
*   *[谷歌 Oauth 登录旁路](https://github.com/ngalongc/bug-bounty-reference#google-oauth-bypass)*
*   *[服务器端请求伪造(SSRF)](https://github.com/ngalongc/bug-bounty-reference#server-side-request-forgery-ssrf)*
*   *[无限制文件上传](https://github.com/ngalongc/bug-bounty-reference#unrestricted-file-upload)*
*   *[竞争条件](https://github.com/ngalongc/bug-bounty-reference#race-condition)*
*   *[业务逻辑缺陷](https://github.com/ngalongc/bug-bounty-reference#business-logic-flaw)*
*   *[认证旁路](https://github.com/ngalongc/bug-bounty-reference#authentication-bypass)*
*   *[HTTP 头注入](https://github.com/ngalongc/bug-bounty-reference#http-header-injection)*
*   *[邮件相关](https://github.com/ngalongc/bug-bounty-reference#email-related)*
*   *[偷钱](https://github.com/ngalongc/bug-bounty-reference#money-stealing)*
*   *[杂项](https://github.com/ngalongc/bug-bounty-reference#miscellaneous)*

## *跨站点脚本(XSS)*

*   *沉睡的谷歌 XSS 唤醒 5000 美元赏金*
*   *[RPO 导致谷歌](http://blog.innerht.ml/rpo-gadgets/)文件描述符信息泄露*
*   *上帝般的 XSS，登录，注销，登录杰克·惠顿在优步*
*   *三个储存在脸书的 XSS*
*   *[使用博朗剃须刀绕过 XSS 审计和晶圆](https://blog.bugcrowd.com/guest-blog-using-a-braun-shaver-to-bypass-xss-audit-and-waf-by-frans-rosen-detectify)Frans Rosen*
*   *杰克·惠顿的《脸书的 XSS》*
*   *他能够将存储的 XSS 从不相关的域转移到 facebook 的主域*
*   *[杰克·惠顿将 XSS 储存在*.ebay.com](https://whitton.io/archive/persistent-xss-on-myworld-ebay-com/) 中*
*   *[复杂，谷歌 XSS 最佳报告](https://sites.google.com/site/bughunteruniversity/best-reports/account-recovery-xss)作者 Ramzes*
*   *[棘手的 Html 注入和 sms-be-vip.twitter.com 可能的 XSS](https://hackerone.com/reports/150179)*
*   *[Venkat S 在 Google 控制台](http://www.pranav-venkat.com/2016/03/command-injection-which-got-me-6000.html)中的命令注入*
*   *[脸书的举动——乌思·XSS](http://www.paulosyibelo.com/2015/12/facebooks-moves-oauth-xss.html)保罗·伊贝罗*
*   *[谷歌文档中存储的 XSS(Bug Bounty)](http://hmgmakarovich.blogspot.hk/2015/11/stored-xss-in-google-docs-bug-bounty.html)哈利·M·格托斯*
*   *[James Kettle(albino wax)在优步通过管理员帐户泄露将 XSS 存储在 developer.uber.com](https://hackerone.com/reports/152067)*
*   *雅虎邮箱储存的 XSS*
*   *[滥用 XSS 滤镜:一个^导致 XSS(CVE-2016–3212)](http://mksben.l0.cm/2016/07/xxn-caret.html)魔裟斗·木川浩*
*   *弗兰斯罗森的 Youtube XSS*
*   *再次获得最佳谷歌 XSS 奖*
*   *[IE &边缘 URL 解析问题](https://labs.detectify.com/2016/10/24/combining-host-header-injection-and-lax-host-parsing-serving-malicious-data/) —通过检测*
*   *[谷歌 XSS 子域点击劫持](http://sasi2103.blogspot.sg/2016/09/combination-of-techniques-lead-to-dom.html)*
*   *[微软 XSS 和推特 XSS](http://blog.wesecureapp.com/xss-by-tossing-cookies/)*
*   *[谷歌日本图书 XSS](http://nootropic.me/blog/en/blog/2016/09/20/%E3%82%84%E3%81%AF%E3%82%8A%E3%83%8D%E3%83%83%E3%83%88%E3%82%B5%E3%83%BC%E3%83%95%E3%82%A3%E3%83%B3%E3%82%92%E3%81%97%E3%81%A6%E3%81%84%E3%81%9F%E3%82%89%E3%81%9F%E3%81%BE%E3%81%9F%E3%81%BEgoogle/)*
*   *[闪光 XSS 兆新西兰](https://labs.detectify.com/2013/02/14/how-i-got-the-bug-bounty-for-mega-co-nz-xss/)——弗兰斯*
*   *[多个图书馆里的闪电侠 XSS](https://olivierbeg.com/finding-xss-vulnerabilities-in-flash-files/)——奥利维尔·贝格*
*   *[Google IE 中的 xss，主机头反射](http://blog.bentkowski.info/2015/04/xss-via-host-header-cse.html)*
*   *[年前谷歌 xss](http://conference.hitb.org/hitbsecconf2012ams/materials/D1T2%20-%20Itzhak%20Zuk%20Avraham%20and%20Nir%20Goldshlager%20-%20Killing%20a%20Bug%20Bounty%20Program%20-%20Twice.pdf)*
*   *[xss 在谷歌被 IE 怪异行为](http://blog.bentkowski.info/2015/04/xss-via-host-header-cse.html)*
*   *[雅虎幻想运动中的 XSS](http://dawgyg.com/2016/12/07/stored-xss-affecting-all-fantasy-sports-fantasysports-yahoo-com-2/)*
*   *又是雅虎邮箱里的 xss，价值 10000 美元*
*   *[谷歌中沉睡的 XSS](https://blog.it-securityguard.com/bugbounty-sleeping-stored-google-xss-awakens-a-5000-bounty/)安全卫士*
*   *[由 securityguard 解码一个. htpasswd 以赚取金钱的有效载荷](https://blog.it-securityguard.com/bugbounty-decoding-a-%F0%9F%98%B1-00000-htpasswd-bounty/)*
*   *[谷歌账户被接管](http://www.orenh.com/2013/11/google-account-recovery-vulnerability.html#comment-form)*
*   *AirBnb Bug Bounty:把自我 XSS 变成好人——XSS # 2*
*   *[优步自我 XSS 到全球 XSS](https://httpsonly.blogspot.hk/2016/08/turning-self-xss-into-good-xss-v2.html)*
*   *[我是如何找到价值 5000 美元的谷歌地图 XSS 的(通过摆弄 Protobuf)](https://medium.com/@marin_m/how-i-found-a-5-000-google-maps-xss-by-fiddling-with-protobuf-963ee0d9caff#.cktt61q9g) 作者:马林·穆林*
*   *[Airbnb——当绕过 JSON 编码时，XSS 过滤器、WAF、CSP 和 Auditor 变成了八个漏洞](https://buer.haus/2017/03/08/airbnb-when-bypassing-json-encoding-xss-filter-waf-csp-and-auditor-turns-into-eight-vulnerabilities/)*
*   *[XSSI，客户端暴力破解](http://blog.intothesymmetry.com/2017/05/cross-origin-brute-forcing-of-saml-and.html)*
*   *[XSS 绕道](https://hackerone.com/reports/231053)*
*   *zhchbin 制作的饼干 XSS 在优步*
*   *[Frans 使用 Marketo Forms XSS 通过 postMessage 框架跳转和 jQuery-JSONP](https://hackerone.com/reports/207042) 窃取 www.hackerone.com 的联系人表单数据*
*   *[XSS 由于第三方 js 优步 7k XSS 的不正确正则表达式](http://zhchbin.github.io/2016/09/10/A-Valuable-XSS/)*
*   *[XSS 在 TinyMCE 2.4.0](https://hackerone.com/reports/262230) 由杰尔默·德·亨*
*   *[在 IE11 中传递未编码的 URL 以导致 XSS](https://hackerone.com/reports/150179)*
*   *[Twitter XSS 停止重定向和 javascript 方案](http://blog.blackfan.ru/2017/09/devtwittercom-xss.html)Sergey Bobrov*

## *蛮力*

*   *[Web 身份验证端点凭证暴力漏洞](https://hackerone.com/reports/127844)作者 Arne Swinnen*
*   *《InstaBrute:两种暴力破解 Instagram 账户凭证的方法》阿恩·斯温嫩著*
*   *[我如何能泄露 4%(锁定)的 Instagram 账户](https://www.arneswinnen.net/2016/03/how-i-could-compromise-4-locked-instagram-accounts/)*
*   *[r0t 在 riders.uber.com 暴力破解邀请码的可能性](https://hackerone.com/reports/125505)*
*   *[partners.uber.com 的强力邀请码](https://hackerone.com/reports/144616)作者:埃夫坎·戈巴(mefkan)*
*   *我是如何黑掉所有脸书账户的*
*   *[脸书帐户通过使用短信验证码接管，目前无法访问，以后可能会从作者处获得更新](http://arunsureshkumar.me/index.php/2016/04/24/facebook-account-take-over/)Arun Sureshkumar*

## *SQL 注入*

*   *[SQL 注入在 WordPress 插件巨大的 IT 视频画廊优步](https://hackerone.com/reports/125932)由 glc*
*   *sctrack.email.uber.com.cn 上的 SQL 注入*
*   *[雅虎— Root 访问 SQL 注入—tw.yahoo.com](http://buer.haus/2015/01/15/yahoo-root-access-sql-injection-tw-yahoo-com/)作者 Brett Buerhaus*
*   *Abood Nour (syndr0me)在 drive.uber.com 的一个 WordPress 插件中的多个漏洞*
*   *[GitHub 企业 SQL 注入](http://blog.orange.tw/2017/01/bug-bounty-github-enterprise-sql-injection.html)由 Orange*

## *窃取访问令牌*

*   *[杰克·惠顿盗窃了脸书访问令牌](https://whitton.io/articles/stealing-facebook-access-tokens-with-a-double-submit/)*
*   *[获得 Outlook、Office 或 Azure 帐户的登录令牌](https://whitton.io/articles/obtaining-tokens-outlook-office-azure-account/)作者杰克·惠顿*
*   *[通过文件描述符使用 HPP](https://hackerone.com/reports/114169) 绕过数字 web 认证的主机验证*
*   *[使用/绕过 redirect_uri 验证..叶戈尔·霍马科夫著](http://homakov.blogspot.hk/2014/02/how-i-hacked-github-again.html?m=1)*
*   *[通过文件描述符绕过数字](https://hackerone.com/reports/108113)的回调 _url 验证*
*   *[窃取 livechat 令牌并使用它作为用户聊天——用户信息泄露](https://hackerone.com/reports/151058)Mahmoud g .(zombiehelp 54)*
*   *[通过/rt/users/passless-sign up-Account take over(critical)](https://hackerone.com/reports/143717)由 mongo (mongo)更改任何优步用户的密码*
*   *[Internet Explorer 有一个 URL 问题，在 GitHub 上](http://blog.innerht.ml/internet-explorer-has-a-url-problem/)由 filedescriptor。*
*   *[我如何让 LastPass 给我你所有的密码](https://labs.detectify.com/2016/07/27/how-i-made-lastpass-give-me-all-your-passwords/)通过 labsdetectify*
*   *[在微软窃取谷歌 Oauth](http://blog.intothesymmetry.com/2015/06/on-oauth-token-hijacks-for-fun-and.html)*
*   *[盗取 FB 接入令牌](http://blog.intothesymmetry.com/2014/04/oauth-2-how-i-have-hacked-facebook.html)*
*   *[Paypal 访问令牌泄露](http://blog.intothesymmetry.com/2016/11/all-your-paypal-tokens-belong-to-me.html?m=1)*
*   *[窃取 FB 访问令牌](http://homakov.blogspot.sg/2013/02/hacking-facebook-with-oauth2-and-chrome.html)*
*   *[Appengine 酷炫 Bug](https://proximasec.blogspot.hk/2017/02/a-tale-about-appengines-authentication.html)*
*   *[松弛帖留言真实生活体验](https://labs.detectify.com/2017/02/28/hacking-slack-using-postmessage-and-websocket-reconnect-to-steal-your-precious-token/)*
*   *[通过 nbsriharsha 绕过 redirect_uri](http://nbsriharsha.blogspot.in/2016/04/oauth-20-redirection-bypass-cheat-sheet.html)*
*   *[盗取价值 15k 的 Facebook Messenger nonce】](https://stephensclafani.com/2017/03/21/stealing-messenger-com-login-nonces/)*

## ***谷歌 oauth 旁路***

*   *绕过潜望镜管理面板上的谷歌认证*

## *CSRF*

*   *杰克·惠顿的《Messenger.com·CSRF 向你展示查找 CSRF 的步骤》*
*   *[Paypal bug bounty:未经同意更新 Paypal.me 个人资料图片(CSRF 攻击)](https://hethical.io/paypal-bug-bounty-updating-the-paypal-me-profile-picture-without-consent-csrf-attack/)作者 Florian Courtial*
*   *[一键入侵贝宝账户(补丁)](http://yasserali.com/hacking-paypal-accounts-with-one-click/)亚瑟·阿里*
*   *将推文添加到维贾伊·库马尔的 CSRF 作品集*
*   *Facebook marketing developers . com:proxy，CSRF Quandry 和 API Fun 由 phwd 提供*
*   *[我怎么黑了你的 Beats 账户？苹果虫赏金](https://aadityapurani.com/2016/07/20/how-i-hacked-your-beats-account-apple-bug-bounty/)作者@aaditya_purani*

## *远程代码执行*

*   *[JDWP 远程贝宝代码执行](https://www.vulnerability-lab.com/get_content.php?id=1474)米兰·索兰基*
*   *《OpenID 中的 XXE:一个错误统治所有人，或者我如何发现一个影响脸书服务器的远程代码执行缺陷》作者雷吉纳多·希尔瓦*
*   *[我如何黑了脸书，发现了某人的后门脚本](http://devco.re/blog/2016/04/21/how-I-hacked-facebook-and-found-someones-backdoor-script-eng-ver/)橘仔*
*   *[我如何链接 GitHub Enterprise 上的 4 个漏洞，从 SSRF 执行链到 RCE！](http://blog.orange.tw/2017/07/how-i-chained-4-vulnerabilities-on.html)蔡橙*
*   *[uber.com 梅 RCE 通过烧瓶注射模板](https://hackerone.com/reports/125980)蔡橙通过*
*   *[雅虎 Bug Bounty — *.login.yahoo.com 远程代码执行](http://blog.orange.tw/2013/11/yahoo-bug-bounty-part-2-loginyahoocom.html)由 Orange Tsai(抱歉只有中文)*
*   *[我们是如何破解 PHP、入侵 Pornhub 并赚到 20，000 美元的](https://www.evonide.com/how-we-broke-php-hacked-pornhub-and-earned-20000-dollar/)*
*   **预警*，神一样的写上去，点之前一定要知道什么是 ROP，这个我不知道=(*
*   *[RCE 交易到棘手文件上传](https://www.secgeek.net/bookfresh-vulnerability/)*
*   *[WordPress plupload . flash . swf 中的一些 bug 导致 Cure53 (cure53)自动](https://hackerone.com/reports/134738)RCE*
*   *只读用户可以通过 93c08539 (93c08539)在 AirOS 上执行仲裁 shell 命令*
*   *[通过 impage 上传远程执行代码！](https://hackerone.com/reports/158148)作者 Raz0r (ru_raz0r)*
*   *Bitquark 在 Oculus 开发者门户网站上弹出一个外壳*
*   *[疯了！又是 PornHub RCE！！！我是如何为了乐趣和利益黑 Pornhub 的——10，000 美元](https://5haked.blogspot.sg/)*
*   *[PayPal Node.js 代码注入(RCE)](http://artsploit.blogspot.hk/2016/08/pprce2.html) 作者 Michael Stepankin*
*   *[易贝 PHP 参数注入导致 RCE](http://secalert.net/#ebay-rce-ccs)*
*   *雅虎收购 RCE*
*   *Hostinger 中的命令注入漏洞*
*   *[RCE 在 Airbnb 被红宝石注入](http://buer.haus/2017/03/13/airbnb-ruby-on-rails-string-interpolation-led-to-remote-code-execution/)被 buerRCE*
*   *[RCE 在 Imgur 中通过命令行](https://hackerone.com/reports/212696)*
*   *[git.imgur.com RCE 滥用过时软件](https://hackerone.com/reports/206227)蔡橙*
*   *[RCE 在披露](https://hackerone.com/reports/213558)*
*   *[struct 2 雅虎服务器远程执行代码](https://medium.com/@th3g3nt3l/how-i-got-5500-from-yahoo-for-rce-92fffb7145e6)*
*   *[雅虎收购案中的指令注入](http://samcurry.net/how-i-couldve-taken-over-the-production-server-of-a-yahoo-acquisition-through-command-injection/)*
*   *[贝宝 RCE](http://blog.pentestbegins.com/2017/07/21/hacking-into-paypal-server-remote-code-execution-2017/)*
*   *JetBrains IDE 中的$50k RCE*
*   *$20k RCE 在 Jenkin 实例中*

## ***反序列化***

*   *manager.paypal.com 的 Java 反序列化*
*   *[Instagram 的百万美元 Bug](http://www.exfiltrated.com/research-Instagram-RCE.php) 韦斯利·温伯格*
*   *米歇尔·普林斯(Michiel Prins)的《facebooksearch.algolia.com 上的 RCE》*
*   *[Java 反序列化](https://seanmelia.wordpress.com/2016/07/22/exploiting-java-deserialization-via-jboss/)按餐*

## *图像 Tragick*

*   *[利用 ImageMagick 让 RCE 参与 Polyvore(雅虎收购)](http://nahamsec.com/exploiting-imagemagick-on-yahoo/)NaHamSec*
*   *[用 c666a323be94d57 将 RCE 放在钢骨](https://hackerone.com/reports/135072)上*
*   *[Trello bug bounty:使用 Florian Courtial 的 ImageTragick](https://hethical.io/trello-bug-bounty-access-servers-files-using-imagetragick/) 访问服务器文件*
*   *[40k fb rce](https://github.com/ngalongc/bug-bounty-reference/blob/master/4lemon.ru/2017-01-17_facebook_imagetragick_remote_code_execution.html)*
*   *[雅虎出血 1](https://scarybeastsecurity.blogspot.hk/2017/05/bleed-continues-18-byte-file-14k-bounty.html)*
*   *[雅虎出血 2](https://scarybeastsecurity.blogspot.hk/2017/05/bleed-more-powerful-dumping-yahoo.html)*

## *不安全直接对象引用(IDOR)*

*   *Trello bug bounty:当一家上市公司创建一个团队可视板时，websocket 接收数据*
*   *Trello bug bounty:当一个团队改变其可见性时，Florian Courtial 会向 webhook 发送支付信息*
*   *[通过 mongo 在优步更改任何用户的密码](https://hackerone.com/reports/143717)*
*   *Youtube 中的一个漏洞允许 secgeek 将评论从任何视频移动到另一个视频*
*   *这是谷歌的漏洞，所以值得一读，因为一般来说发现谷歌漏洞更困难*
*   *[推特漏洞可以从任何推特账户上刷卡](https://www.secgeek.net/twitter-vulnerability/)由 secgeek*
*   *[一个漏洞允许 secgeek 删除所有雅虎网站](https://www.secgeek.net/yahoo-comments-vulnerability/)中任何用户的评论*
*   *[Microsoft-careers.com 远程密码重置](http://yasserali.com/microsoft-careers-com-remote-password-reset/)由亚瑟·阿里完成*
*   *我如何能改变你的易贝密码*
*   *[Duo 安全研究人员发现 Duo 实验室绕过 PayPal 的双因素认证](https://duo.com/blog/duo-security-researchers-uncover-bypass-of-paypal-s-two-factor-authentication)*
*   *[黑客代表你的朋友 Facebook.com/thanks 发帖！](http://www.anandpraka.sh/2014/11/hacking-facebookcomthanks-posting-on.html)作者阿南·普拉卡什*
*   *[我是如何访问数百万个[被编辑的]账户的](https://bitquark.co.uk/blog/2016/02/09/how_i_got_access_to_millions_of_redacted_accounts)*
*   *[Enguerran Gillier(OPN sec)通过授权旁路披露所有 Vimeo 私人视频，并提供出色的技术说明](https://hackerone.com/reports/137502)*
*   *[紧急:攻击者可以通过 Jobert Abma (jobert)访问 Bime](https://hackerone.com/reports/149907) 上的所有数据源*
*   *由 Gazza 在 Vimeo 上下载受密码保护/限制的视频*
*   *[通过 Severus(西弗勒斯)基于优步的 uuid 获取组织信息](https://hackerone.com/reports/151465)*
*   *[我是如何暴露你在脸书的主要电子邮件地址的(价值 4500 美元的窃听器)](http://roy-castillo.blogspot.hk/2013/07/how-i-exposed-your-primary-facebook.html)作者罗伊·卡斯蒂洛*
*   *[由 Raja Sekar Durairaj 使用“脸书图形 API 逆向工程”披露的 DOB](https://medium.com/@rajsek/my-3rd-facebook-bounty-hat-trick-chennai-tcs-er-name-listed-in-facebook-hall-of-fame-47f57f2a4f71#.9gbtbv42q)*
*   *[由 phwd 修改脸书](http://philippeharewood.com/change-the-description-of-a-video-without-publish_actions-permission/)中没有 publish_actions 权限的视频的描述*
*   *[响应请求注射(RTRI)](https://www.bugbountyhq.com/front/latestnews/dWRWR0thQ2ZWOFN5cTE1cXQrSFZmUT09/) 通过？，说实话，感谢这篇文章，我因为使用他的方法发现了相当多的 bug，尊重作者！*
*   *[泄露所有项目名称和所有用户名，甚至跨应用程序](https://hackerone.com/reports/152696)*
*   *预订旅行时更改 paymentProfileUuid 可以免费乘坐优步*
*   *[查看私人推文](https://hackerone.com/reports/174721)*
*   *[优步 Enum UUID](http://www.rohk.xyz/uber-uuid/)*
*   *[破解脸书的遗留 API，第 1 部分:代表任何用户进行调用](http://stephensclafani.com/2014/07/08/hacking-facebooks-legacy-api-part-1-making-calls-on-behalf-of-any-user/)作者 Stephen Sclafani*
*   *[黑客攻击脸书的遗留 API，第 2 部分:窃取用户会话](http://stephensclafani.com/2014/07/29/hacking-facebooks-legacy-api-part-2-stealing-user-sessions/)*
*   *[删除 FB 视频](https://danmelamed.blogspot.hk/2017/01/facebook-vulnerability-delete-any-video.html)*
*   *[删除 FB 视频](https://pranavhivarekar.in/2016/06/23/facebooks-bug-delete-any-video-from-facebook/)*
*   *[脸书通过操纵参数接管页面](http://arunsureshkumar.me/index.php/2016/09/16/facebook-page-takeover-zero-day-vulnerability/)*
*   *[查看私人 Airbnb 消息](http://buer.haus/2017/03/31/airbnb-web-to-app-phone-notification-idor-to-view-everyones-airbnb-messages/)*
*   *kedrisec 的 IDOR tweet as any user*
*   *[Twitter 中的经典 IDOR 端点](http://www.anandpraka.sh/2017/05/how-i-took-control-of-your-twitter.html)*
*   *sean 的[批量分配、请求注入响应、管理升级](https://seanmelia.wordpress.com/2017/06/01/privilege-escalation-in-a-django-application/)*

## *XXE*

*   *[我们如何通过 detectify 获得谷歌生产服务器的读取权限](https://blog.detectify.com/2014/04/11/how-we-got-read-access-on-googles-production-servers/)*
*   *[盲人 OOB·XXE 在优步 26+域名被 Raghav Bisht 黑](http://nerdint.blogspot.hk/2016/08/blind-oob-xxe-at-uber-26-domains-hacked.html)*
*   *[XXE 穿越 SAML](https://seanmelia.files.wordpress.com/2016/01/out-of-band-xml-external-entity-injection-via-saml-redacted.pdf)*
*   *[XXE 在优步阅读当地文件](https://httpsonly.blogspot.hk/2017/01/0day-writeup-xxe-in-ubercom.html)*
*   *[community.lithium.com SVG 的 XXE](http://esoln.net/Research/2017/03/30/xxe-in-lithium-community-platform/)*

## *无限制文件上传*

*   *[vijay Kumar 在 mopub](https://hackerone.com/reports/97672) 上传 App 图片时上传 XSS 文件*
*   *[RCE 处理棘手文件上传](https://www.secgeek.net/bookfresh-vulnerability/)由 secgeek*
*   *[vijay Kumar(vijay _ Kumar 1110)在 Twitter 中的 mopub 中的 App 的图片上传中的文件上传 XSS](https://hackerone.com/reports/97672)*

## *服务器端请求伪造(SSRF)*

*   *[ESEA 服务器端请求伪造和查询 AWS 元数据](http://buer.haus/2016/04/18/esea-server-side-request-forgery-and-querying-aws-meta-data/)*
*   *[SSRF 至枢内部网络](https://seanmelia.files.wordpress.com/2016/07/ssrf-to-pivot-internal-networks.pdf)*
*   *[SSRF 到 LFI](https://seanmelia.wordpress.com/2015/12/23/various-server-side-request-forgery-issues/)*
*   *[SSRF 要查询谷歌内部服务器](https://www.rcesecurity.com/2017/03/ok-google-give-me-all-your-internal-dns-information/)*
*   *[SSRF 利用第三方开放重定向](https://buer.haus/2017/03/09/airbnb-chaining-third-party-open-redirect-into-server-side-request-forgery-ssrf-via-liveperson-chat/)布雷特·布尔豪斯*
*   *[SSRF 提示来自 BugBountyHQ 的图片](https://twitter.com/BugBountyHQ/status/868242771617792000)*
*   *[SSRF 到 RCE](http://www.kernelpicnic.net/2017/05/29/Pivoting-from-blind-SSRF-to-RCE-with-Hashicorp-Consul.html)*
*   *[XXE 在推特上](https://hackerone.com/reports/248668)*
*   *[博文:破解镜头:瞄准 HTTP 的隐藏攻击面](http://blog.portswigger.net/2017/07/cracking-lens-targeting-https-hidden.html)*

## *竞态条件*

*   *[脸书、数字海洋和其他公司的比赛条件(固定)](http://josipfranjkovic.blogspot.hk/2015/04/race-conditions-on-facebook.html)Josip franjkovi*
*   *由法比奥·皮雷斯(shmoo)撰写的 HackerOne 中的热门报道中的种族状况*

## *业务逻辑缺陷*

*   *[脸书简单的技术破解查看时间线](http://ashishpadelkar.com/index.php/2015/09/23/facebook-simple-technical-bug-worth-7500/)Ashish Padelkar*
*   *我如何从 Instagram、谷歌和微软偷钱*
*   *[我怎么会把你所有的脸书笔记都拿走了](http://www.anandpraka.sh/2015/12/summary-this-blog-post-is-about.html)*
*   *[脸书—2015 年绕过 ads 帐户的角色漏洞](http://blog.darabi.me/2015/03/facebook-bypass-ads-account-roles.html)作者 POUYA DARABI*
*   *阿南德·普拉卡免费乘坐优步*
*   *[优步吃白食](https://t.co/MCOM7j2dWX)靠*

## *认证旁路*

*   *Jouko pynnen(Jouko)在优步通过 XMLRPC 在 WordPress 网站上绕过 OneLogin 认证*
*   *henryhoggard 的 PayPal 旁路*
*   *[Github 中价值 15000 的 SAML Bug](http://www.economyofmechanism.com/github-saml.html)*
*   *[通过 OAuth 令牌盗窃在 Airbnb 上绕过认证](https://www.arneswinnen.net/2017/06/authentication-bypass-on-airbnb-via-oauth-tokens-theft/)*
*   *[优步登陆 CSRF +打开重定向- >优步账户接管](http://ngailong.com/uber-login-csrf-open-redirect-account-takeover/)*
*   *【[http://c 0 rni 3 sm . blogspot . hk/2017/08/incidentially-typo-to-bypass . html？m=1](管理](http://c0rni3sm.blogspot.hk/2017/08/accidentally-typo-to-bypass.html?m=1](Administrative)面板访问)通过 c0rni3sm*
*   *优步虫赏金:进入内部聊天系统*

## *HTTP 头注入*

*   *[Twitter 溢出三部曲中的 Twitter](https://blog.innerht.ml/overflow-trilogy/) by filedescriptor*
*   *[推特 CRLF](https://blog.innerht.ml/twitter-crlf-injection/) 通过文件描述符*
*   *[谷歌的 Adblock Plus 和(一点点)more](https://adblockplus.org/blog/finding-security-issues-in-a-website-or-how-to-get-paid-by-google)*
*   *[$ 10k host header](https://sites.google.com/site/testsitehacking/10k-host-header)Ezequiel Pereira*

## *子域接管*

*   *[geek boy 劫持大量 Instapage 过期用户域名&子域名](http://www.geekboy.ninja/blog/hijacking-tons-of-instapage-expired-users-domains-subdomains/)*
*   *[在优步子域阅读邮件](https://hackerone.com/reports/156536)*
*   *松弛的虫子之旅——大卫·维埃拉-库尔茨*
*   *[子域接管并将其链接以执行身份验证绕过](https://www.arneswinnen.net/2017/06/authentication-bypass-on-ubers-sso-via-subdomain-takeover/)Arne Swinnen*

## *作者向上写*

*   *[雅虎支付漏洞](http://ngailong.com/abusing-multistage-logic-flaw-to-buy-anything-for-free-at-hk-deals-yahoo-com/)*
*   *[绕过谷歌电子邮件域检查，代表谷歌发送垃圾邮件](http://ngailong.com/bypassing-google-email-domain-check-to-deliver-spam-email-on-googles-behalf/)*
*   *[当服务器端请求伪造结合跨站脚本](http://ngailong.com/what-could-happen-when-server-side-request-forgery-combine-with-cross-site-scripting/)*

## *XSSI*

*   *[XSSI 纯文本阅读](http://balpha.de/2013/02/plain-text-considered-harmful-a-cross-domain-exploit/)*
*   *[JSON 劫持](http://blog.portswigger.net/2016/11/json-hijacking-for-modern-web.html)*
*   *[OWASP XSSI](https://www.owasp.org/images/f/f3/Your_Script_in_My_Page_What_Could_Possibly_Go_Wrong_-_Sebastian_Lekies%2BBen_Stock.pdf)*
*   *[日本基于标识符的 XSSI 攻击](http://www.mbsd.jp/Whitepaper/xssi.pdf)*
*   *[JSON 劫持幻灯片](https://www.owasp.org/images/6/6a/OWASPLondon20161124_JSON_Hijacking_Gareth_Heyes.pdf)*

## *电子邮件相关*

*   *[此域是我的域— G Suite A 记录漏洞](http://blog.pentestnepal.tech/post/156959105292/this-domain-is-my-domain-g-suite-a-record)*
*   *[我收到电子邮件— G 套件漏洞](http://blog.pentestnepal.tech/post/156707088037/i-got-emails-g-suite-vulnerability)*
*   *[我是如何窥探你的私人 Slack 消息的【价值 2500 美元的 Slack Bug 赏金】](http://blog.pentestnepal.tech/post/150381068912/how-i-snooped-into-your-private-slack-messages)*
*   *[阅读优步内部邮件【价值 1 万美元的优步 Bug 赏金报告】](http://blog.pentestnepal.tech/post/149985438982/reading-ubers-internal-emails-uber-bug-bounty)*
*   *利用 TicketTrick 收购 Slack Yammer*
*   *我怎么能从每一个 Flickr 账户上批量上传呢！*

## *偷钱*

*   *[回合错误问题- >由 4lemon 在比特币网站](https://hackerone.com/reports/176461)免费生产货币*

## *2017 本地文件包含*

*   *[Symlink 泄露本地文件包含](https://hackerone.com/reports/213558)*
*   *[脸书符号链接本地文件包含](http://josipfranjkovic.blogspot.hk/2014/12/reading-local-files-from-facebooks.html)*
*   *[Gitlab Symlink 本地文件包含](https://hackerone.com/reports/158330)*
*   *[Gitlab Symlink 本地文件包含第二部分](https://hackerone.com/reports/178152)*
*   *[多家公司 LFI](http://panchocosil.blogspot.sg/2017/05/one-cloud-based-local-file-inclusion.html)*
*   *[LFI 通过视频转换，对这个把戏感到兴奋！](https://hackerone.com/reports/226756)*

## ***杂项***

*   *[SAML 笔测试好的试卷](http://research.aurainfosec.io/bypassing-saml20-SSO/)*
*   *[phwd 收集的 FB 评论列表](https://www.facebook.com/notes/phwd/facebook-bug-bounties/707217202701640)phwd*
*   *[NoSQL 注射液](http://blog.websecurify.com/2014/08/hacking-nodejs-and-mongodb.html)由 websecurify*
*   *[CORS 在行动](http://www.geekboy.ninja/blog/exploiting-misconfigured-cors-cross-origin-resource-sharing/)*
*   *[Fb 信使中的 CORS](http://www.cynet.com/blog-facebook-originull/)*
*   *[网络应用方法](https://blog.zsec.uk/ltr101-method-to-madness/)*
*   *[XXE 小抄](https://www.silentrobots.com/blog/2015/12/14/xe-cheatsheet-update/)*
*   *[通往地狱的道路是用 SAML 断言铺成的，微软漏洞](http://www.economyofmechanism.com/office365-authbypass.html#office365-authbypass)*
*   *如果你想学习 cirw 的 Mongo SQL 注入,就学习这个吧*
*   *[Mongo DB 再次注入](http://blog.websecurify.com/2014/08/hacking-nodejs-and-mongodb.html)由 websecrify*
*   *[w3af 关于现代脆弱性的演讲](https://www.youtube.com/watch?v=GNU0_Uzyvl0)w3af*
*   *[导致帐户被接管的 Web 缓存攻击](http://omergil.blogspot.co.il/2017/02/web-cache-deception-attack.html)*
*   *[教你如何使用 SAML Raider 的讲座](https://www.usenix.org/conference/usenixsecurity12/technical-sessions/presentation/somorovsky)*
*   *[当你不知道如何利用漏洞时的 XSS 清单](http://d3adend.org/xss/ghettoBypass)*
*   *[CTF 写上去，为臭虫悬赏大](https://ctftime.org/writeups?tags=web200&hidden-tags=web%2cweb100%2cweb200)*
*   *事实证明，每个使用 jquery mobile 的开放重定向网站都容易受到 sirdarckcat 的 XSS 攻击*
*   *[通过使用 google-analytics 绕过 CSP](https://hackerone.com/reports/199779)*
*   *[Paypal 支付问题](https://hackerone.com/reports/219215)*
*   *[中文浏览器开发](http://paper.seebug.org/)*
*   *[XSS 旁路过滤器](https://t.co/0Kpzo52ycb)*
*   *[标记改进清理](https://github.com/ChALkeR/notes/blob/master/Improper-markup-sanitization.md)*
*   *[通过脚本小工具破解 XSS 缓解措施](https://www.blackhat.com/docs/us-17/thursday/us-17-Lekies-Dont-Trust-The-DOM-Bypassing-XSS-Mitigations-Via-Script-Gadgets.pdf)*
*   *[X41 浏览器安全白皮书](https://browser-security.x41-dsec.de/X41-Browser-Security-White-Paper.pdf)*

## *虫子赏金的 10 条规则*

*![](img/e9abf46e70167a983c1b5508c74cc202.png)*

*遵循“**Bug 赏金**10 条规则”*

1.  *瞄准虫子赏金计划*
2.  *你如何接近目标？*
3.  *不要期待什么！*
4.  *关于漏洞和测试方法的知识较少*
5.  *让自己置身于 Bug Bounty 社区，让自己保持最新状态*
6.  *自动化*
7.  *获得奖金或获得经验*
8.  *找到“错误”或“错误链”*
9.  *跟随师父的道路*
10.  *放松和享受生活*

*好了，伙计们，希望我的基本侦察方法可以帮助你正确选择目标，正确绘制地图，使用你收集的信息追捕它，最后写一份报告建议阅读博客[https://blog . bug crowd . com/advice-for-Writing-a-great-vulnerability-Report/](https://blog.bugcrowd.com/advice-for-writing-a-great-vulnerability-report/)*

*我为你的帮助创建了一个完整的资源列表😉([***https://github . com/husnainfareed/Resources-for-learning-ethical-hacking/***](https://github.com/hussnainfareed007/Resources-for-learning-ethical-hacking/)*)**

*![](img/53c5d02fb3063527fbb6c943c09e92fa.png)*

*#请注意，所有参考资料都来自互联网，并在互联网上分享 xD 感谢那些之前分享他们观点的人，这帮助了我学习😉*

## *如果你喜欢这个故事，请点击👏按钮并分享，以帮助其他人找到它。你也可以[给我买杯咖啡](https://www.buymeacoffee.com/hussnainfareed)帮我给你写更多有趣的内容！*