# 让我们深入研究 OSINT:第 1 部分

> 原文：<https://infosecwriteups.com/lets-go-deep-into-osint-part-1-c2de4fe4f3bf?source=collection_archive---------0----------------------->

TLDR 博客将由两部分组成。在第一部分中，我们讨论了 osint 以及在 infosec 中使用的各种资源，在第二篇博客中，我们将探讨 OSINT 面临的一些挑战。

**这个博客是对 OSINT 的简要介绍。它讲述了 OSINT 是关于如何执行它的。**

**开源情报** ( **OSINT** )是从公开来源收集的数据，用于情报领域。在情报界，“公开”一词指的是公开的、公众可获得的来源(与秘密或秘密来源相对)。它与开源软件或集体智慧无关。

**开源情报** ( **OSINT** )是从公共来源收集的信息，如互联网上的信息，尽管该术语并不严格限于互联网，而是指所有公共来源。

**“OS**”(来自 OSINT)意思是**开源**。在这种情况下，它与著名的[开源运动][34]无关，而是与用户可以从他们的情报数据收集中获得信息的任何公开来源有关。

OSINT 概念背后的关键词是**信息**，最重要的是，可以免费获取的信息。只要它是**公开、免费和合法的**，它是否位于报纸、博客、网页、推文、社交媒体卡、图像、播客或视频中并不重要。

掌握了正确的信息，你就能在竞争中获得巨大的优势，或者加快你负责的任何公司/个人的调查。

这很简单，因为它是

*   在任何搜索引擎上提问。
*   研究如何修理你的计算机的公共论坛。
*   观看 youtube 上关于如何制作生日蛋糕的视频。

**信息网络安全**

虽然有很多新的技术和机制，但并不是所有的都适合你的目标。首先，你必须问自己几个问题:

*   我在找什么？
*   我的主要研究目标是什么？
*   我的目标是什么或谁？
*   我将如何进行我的研究？

试着找到这些问题的答案，这将是你调查的第一步。

虽然许多 OSINT 技术被政府和军事机构使用，但它们也可以应用到你自己的公司。有些可能有效，有些可能无效，但这是 OSINT 策略的一部分——你必须识别哪些来源是好的，哪些与你的研究无关。

让我们来看看网络安全中最流行的 OSINT 技术:

*   收集员工的全名、工作角色以及他们使用的软件。
*   查看和监控来自谷歌、必应、雅虎和其他搜索引擎的信息。
*   监控个人和公司博客，以及审查用户在数字论坛上的活动。
*   确定目标用户或公司使用的所有社交网络。
*   查看脸书、Twitter、Google Plus 或 Linkedin 等社交网络上的内容。
*   从谷歌访问旧的缓存数据——经常会发现有趣的信息。
*   识别手机号码，以及社交网络或谷歌搜索结果中的邮件地址。
*   在常见的社交照片分享网站上搜索照片和视频，如 Flickr、Google Photos 等。
*   使用谷歌地图和其他公开的卫星图像来源来检索用户地理位置的图像。

这些是你会发现的一些最受欢迎的技术。然而，在你完成 OSINT 研究后，你会有很多数据要分析。这时你将不得不细化你的结果，详细搜索你需要的所有真正必要的东西，并丢弃其余的。

OSINT 战略的最后一步是将所有这些数字情报数据转化为人类可读的格式，以便非技术人员能够理解，这些人通常是大多数公司的负责人。

因为整个互联网都是你的朋友。所以你对你的目标来说是致命且危险的。

*   假设你想对你的目标进行一次社会工程攻击，现在你想发送一封包含恶意附件的邮件，你计划以其中一名员工为目标，但是等等，你只知道这家公司，不知道它的员工。在互联网时代到来之前，你需要去公司总部周围，然后核实员工，但现在你需要做的只是在谷歌上搜索该公司，可能它自己的网站会显示一些员工的名字，如经理和其他团队成员，这是在笔记本上记录下来的好时机。之后，让我们转到该公司的 linkedin 个人资料，它包含员工部分，您可以在其中看到那些将他们的职位放在个人资料中的用户。所以现在你有了公司员工。大多数情况下，你会从 linkedin 获得尽可能多的员工，所以这是搜索公司员工的最佳地点。现在，转到员工个人资料后，您可以获得他的电子邮件地址来发送电子邮件。

![](img/36c72a9ff190ab56803e4cc5f7ece2ba.png)

我们可以看到特斯拉汽车公司的 458 名员工。这将包括工程师、经理、人力资源、首席执行官、首席技术官等。

*   假设你想进入你的目标的网站或者你的目标的 CMS，因为你可以进行 bug 赏金猎人，那么最好的地方是搜索 github 和 gitlab 库，因为大部分代码是使用版本控制系统如 Git 开发的。请记住，开发人员仍然在存储库中留下许多敏感的凭证，并在公开它们时忘记删除它们。这包括 API 密钥、访问网站的凭据、mysql crendentials、FTP 凭据、ssh 密钥或 ssh 凭据。这里 github 呆子开始发挥作用

例如，在目标公司的 github 回购中查找密码

`"target.com” password`

![](img/8913ef4132c6e36f04c44b5027145013.png)![](img/aa959d3d626c6eac536da599c61b2f1e.png)

用户凭证在 github 上泄露

查找目标的 ftp 或 sftp 凭据

`"target.com" filename:ftpconfig`

`"target.com" filename:sftp-config.json password`

对于 smtp 凭据

`"target.com" filename:.env MAIL_HOST=smtp.gmail.com`

对于 mysql 凭证，搜索您的目标

`"target.com" extension:sql mysql dump password`

这里还有一些呆子

`filename:credentials aws_access_key_id`

`filename:wp-config.php`

`filename:id_rsa`

这里是 github 资源库中关于 OSINT 的更多资源(如果您正在搜索一些关于目标的有趣信息，请务必查看它们)

[](https://securitytrails.com/blog/github-dorks) [## SecurityTrails |用于扫描 GitHub 存储库中敏感数据的顶级 GitHub 工具

### 不幸的是，远程攻击者意识到了这一点。他们认为 GitHub 是一个容易找到暴露的敏感…

securitytrails.com](https://securitytrails.com/blog/github-dorks) [](https://github.com/techgaun/github-dorks/blob/master/github-dorks.txt) [## techgaun/github-dorks

### Permalink GitHub 是 5000 多万开发人员的家园，他们一起工作来托管和审查代码、管理项目以及…

github.com](https://github.com/techgaun/github-dorks/blob/master/github-dorks.txt) 

现在，我们可以自动在 github repo 中查找敏感信息

这里有一些工具

[](https://github.com/michenriksen/gitrob) [## 米切里克森/吉特罗布

### Gitrob 是一个工具，可以帮助找到推送至 Github 上公共存储库的潜在敏感文件。Gitrob 将克隆…

github.com](https://github.com/michenriksen/gitrob) [](https://github.com/dxa4481/truffleHog) [## dxa 4481/松露猪

### 注意了，GitPython 中的一个突破性变化导致了 pip 安装中的一个错误。我会让它工作的…

github.com](https://github.com/dxa4481/truffleHog) [](https://github.com/hisxo/gitGraber) [## hisxo/gitGraber

### gitGraber 是用 Python3 开发的一个工具，用来监控 GitHub 实时搜索和查找敏感数据，用于不同的…

github.com](https://github.com/hisxo/gitGraber) 

这篇文章是关于北卡罗来纳州立大学进行的研究

[](https://www.cyberark.com/blog/github-repositories-leak-thousands-of-secrets-study-shows/) [## 研究显示 GitHub 储存库泄露数千个秘密

### 2019 年 4 月 11 日| Jessica Sirkin 如果您曾经怀疑用户在以下方面对凭据的保护情况…

www.cyberark.com](https://www.cyberark.com/blog/github-repositories-leak-thousands-of-secrets-study-shows/) 

*   Github 和 linkedin 不仅仅是搜寻有趣信息的地方，你还可以使用 ghostlulz 的“bug bounty playbook”中的表格来搜索互联网上各种已知网站的信息

![](img/cf6fe0844413ba28ca679e492368a41e.png)![](img/4a3fa7a6012121c59bf9c7c3b3bae81e.png)![](img/25bb9d3239dc0c6aa78fbf8b60fd63b3.png)

如果任何敏感信息被泄露宾果！！没有进行任何所谓的“黑客攻击”,你就进入了公司。因此，要进入任何公司，有时你不需要成为一名黑客，你需要的只是毅力、寻找的意愿和互联网连接。

*   你可以使用这个叫 shodan 的神奇网站来了解一家公司在互联网上拥有什么，它可以跟踪公司任何与互联网连接的实用程序，如 web 服务器、IOT 设备、蜜罐、防火墙、DNS 服务器、云服务器等。

![](img/aa7e529f8539592aaab6c4e668219a4e.png)

你可以在上面搜索网络摄像头、服务器、物联网设备、数据库

现在也有书呆子为 shodan

假设你想寻找网络摄像头

`Server:SQ-WEBCAM`

![](img/ea92fc346f63b68072ed858a7f22b79e.png)

在探索部分寻找默认为 admin 的相机:admin，Elatic kibana，netcam 和许多其他有趣的东西。

![](img/5a060b080405d4c4911a73cae50df346.png)

通过给出地理坐标来查找设备。
`geo:"56.913055,118.250862"`

查找特定国家的设备。
`country:"IN"`

根据操作系统查找设备。
`os:"windows 7"`

基于开放端口查找设备。
`proftpd port:21`

帮助在 Shodan 找到明文 wifi 密码。
`html:"def_wirelesspassword"`

它可能会给出关于 mongo 数据库服务器和仪表板的信息
`"MongoDB Server Information" port:27017 -authentication`

FTP 服务器匿名访问
`"220" "230 Login successful." port:21`

被入侵的路由器
`hacked-router-help-sos`

查找特定城市的设备。
`city:"Bangalore"`

使用默认密码的设备

`"default password"`

在互联网上查找特定的端口号设备

`port:21`

`port:80`

`port:9200`

现在，您还可以指定要搜索的特定公司

`"company"`

这将使大多数设备连接到该公司的互联网

![](img/8962f55ae497f3a23bf8d3fa17f9efe4.png)

看到它为索尼公司返回了 4，494 个结果

它还提供了该公司使用的顶级服务的详细信息，以及哪些顶级组织正在使用该公司的产品，并向我们提供了目标公司生产的顶级产品。

![](img/f68859525f0b830e5fccbc45233e5ddd.png)

索尼的结果

![](img/59f2fda38b030aa10359b231af6f086a.png)

索尼的结果

现在 shodan 本身就是一个非常广泛的话题，不可能在这里讨论。所以我在这里为 shodan 提供一些资源

[](https://null-byte.wonderhowto.com/how-to/hack-like-pro-find-vulnerable-targets-using-shodan-the-worlds-most-dangerous-search-engine-0154576/) [## 像一个专业黑客:如何找到脆弱的目标使用 Shodan-世界上最危险的搜索…

### 欢迎回来，我的绿角黑客们！有时，我们头脑中没有一个具体的目标，而是我们只是…

null-byte.wonderhowto.com](https://null-byte.wonderhowto.com/how-to/hack-like-pro-find-vulnerable-targets-using-shodan-the-worlds-most-dangerous-search-engine-0154576/) 

*   现在，正如我们所知，我们不能时光旅行回到过去，但我们可以时光旅行回到我们心爱的网站。我们可以用时光倒流机来看看一个网站在特定时间是怎样的

[](https://archive.org/web/) [## 互联网档案:回溯机

### 文本所有书籍所有文本最新这只是在史密森尼图书馆 FEDLINK(美国)家谱林肯收藏

archive.org](https://archive.org/web/) ![](img/3aba6e906ba4fe243327e511e5458250.png)![](img/1cec9dca9734c4d01194ed3c4f4f43b6.png)![](img/d4a83676589e8acb66cf43005ce433ab.png)

它会给我们在那个时间和日期的网站快照。

假设我们需要一个网站上的信息，这个信息三年前就有了，但现在没有了，所以这个信息就派上了用场。

我们也可以使用工具，比如

waybackurl

[](https://github.com/tomnomnom/waybackurls) [## tomnomnom/waybackurls

### 在 stdin 上接受行分隔的域，从 Wayback 机器上为*获取已知的 URL。域并将其输出到…

github.com](https://github.com/tomnomnom/waybackurls) [](https://github.com/Rhynorater/waybacktool) [## Rhynorater/waybacktool

### 一个从 Wayback 机器 API 获取并验证端点存在的工具。

github.com](https://github.com/Rhynorater/waybacktool) 

*   谷歌呆子在 OSINT 中非常重要，因为它可以给我们带来惊人的结果。

**谷歌呆子**涉及使用谷歌搜索引擎中的高级操作符来定位搜索结果中的特定文本字符串。

假设你想搜索一个组织的 pdf 文件，你可以在谷歌搜索中输入。

`"organisation" filetype:pdf`

只需将文件类型更改为您想要搜索的组织的任何类型的文件。

你想要一个公司的登录表格就用

`"company.com" inurl:login`

假设你想找到 wordpress 网站来丑化使用

inurl/WP-content/plugins/rev slider/

`inurl /wp-content/plugins/revslider`

所以每天都有成千上万的谷歌呆子在创造这些东西，这让黑客的工作变得很容易

这里是一些资源

 [## 攻击性安全利用数据库档案

### GHDB 是一个搜索查询索引(我们称之为呆子),用于查找公开可用的信息，旨在…

www.exploit-db.com](https://www.exploit-db.com/google-hacking-database) [](https://gbhackers.com/latest-google-dorks-list/) [## 谷歌呆子名单 2019 -完整的备忘单(新)

### 谷歌呆子名单“谷歌黑客”主要是指拉敏感信息从谷歌使用先进的…

gbhackers.com](https://gbhackers.com/latest-google-dorks-list/) 

[https://gist.github.com/stevenswafford/393c6ec7b5375d5e8cdc](https://gist.github.com/stevenswafford/393c6ec7b5375d5e8cdc)

[](https://www.cybrary.it/blog/0p3n/google-dorks-easy-way-of-hacking/) [## 谷歌呆子:一种简单的黑客方式

### 谷歌搜索引擎为我们的问题找到答案，这对我们的日常生活很有帮助。您可以搜索您的…

www.cybrary.it](https://www.cybrary.it/blog/0p3n/google-dorks-easy-way-of-hacking/) 

*   [https://wigle.net/](https://wigle.net/)它帮助我们找到无线网络的 SSID 或 BSSID 的位置
*   社交网站在 OSINT 中也扮演了一个很好的角色，因为它们可以包含大量关于你所针对的人的信息。

如今，人们在社交媒体上发布关于他们生活的一切，比如他们吃了什么，他们和谁在一起，他们住在哪里，他们和朋友晚上的娱乐场所，他们现在正在哪个餐馆吃饭。

所以，时刻关注你的目标社交媒体账户。您获得的信息可以帮助您进一步策划攻击。

公司员工也经常在工作时拍照，并上传到社交媒体上。这些照片包括暴露的公司批次或代码、暴露他们正在运行的软件和操作系统的桌面照片、可以引导物理攻击的办公室内部结构。

*   有时你得到的只是某样东西的照片，你想搜索这张照片是在哪里拍的或者是什么照片。你可以用 Yandex，google 图片搜索，bing 在网上搜索图片。

![](img/99883281f4cca6397cf1a41bb7f529f6.png)

将照片从你的电脑上传到 Yandex，它会在互联网上的图片中搜索你的图片。

![](img/ab5c4aeaa7afdd73081c531cf97bc29d.png)

谷歌的图片搜索。在谷歌上上传图片，它会在互联网上的图片中进行搜索。

*   图像元数据

**图像元数据**是关于**图像**文件的文本信息，其嵌入到文件中或者包含在与其相关联的单独文件中。**图像元数据**包括与**图像**本身相关的细节以及关于其制作的信息。

有时，我们得到一些图像，其中的元数据可能包含丰富的信息

我们可以使用像 exiftool 这样的工具来获取图像的元数据

![](img/505eeb5f6e24885445ba055735c7efb9.png)

您也可以使用像这样的在线元数据提取器

![](img/dbc55c07d0dd6356bde9a3c337f82351.png)

[http://metapicz.com/#landing](http://metapicz.com/#landing)

*   WHOIS 查找

**WHOIS** (读作短语“WHOIS”)是一种查询和响应协议，广泛用于查询存储互联网资源(如域名、IP 地址块或自治系统)的注册用户或受让人的数据库，但也用于更广泛的其他信息。该协议以人类可读的格式存储和交付数据库内容。WHOIS 协议的当前版本由互联网协会起草，并记录在 RFC 3912 中。

WHOIS 的原始数据可能不会以人类可读的格式存储在数据库中。WHOIS 查找工具将检索数据并将它们按顺序排列，这样我们就可以很容易地理解细节。可以有个人资料，如电子邮件地址，地址等。在 WHOIS 结果中。让我们看看该域的 WHOIS 详细信息的分类。

**WHOIS 详细信息—分类**

WHOIS 详细信息分类如下。

1)域信息

2)注册人联系方式

3)行政联系

4)技术联系

**域名信息**

这种类型的信息包含关于域的一般细节。它将由以下字段组成。每个字段的说明都在字段旁边。

**域名:**该字段将为您提供我们正在查询 WHOIS 详细信息的域名。

**注册商:**这是注册域名的注册商的详细信息。

**注册日期:**这是域名首次注册的日期。使用某些 WHOIS 查找工具时，它会显示为“创建日期”。

**到期日期:**这是域名到期的日期。这个字段可能会有混淆。如果域名已经过期，该字段将在实际过期一年后显示。在这种情况下，我们可以检查“状态”。如果是“持有”或者“赎回”，那么已经过期了。如果域被锁定，我们可以在以下文本中的记录日期集合中找到到期日期。

**更新日期:**这是 WHOIS 详细信息上次更新的日期。

**状态:**这是域名的注册状态。如果没有任何限制，域名可以自由地从一个注册商转移到另一个注册商，这就可以了。

**域名服务器:**该字段将提供域名同时使用的域名服务器的详细信息。

**注册人联系人**

顾名思义，这个区域将为您提供域名注册人的详细信息。顺便问一下，谁是注册人？注册人是注册域名的个人或组织。这只是特定域名的识别细节。注册人的详细信息如下。

1)名称

2)组织

3)街道

4)城市

5)状态

6)邮政编码

7)国家

8)电话

9)传真

10)邮件

**管理联系人**

管理联系人是 Whois 授权与域名注册机构互动的人，负责回答有关域名注册和注册人的问题。该授权将由注册人进行。这个分类中的细节将与上面的完全相似。

**技术联系人**

这是注册人授权管理该域名所有技术问题的个人或组织。他们将收到该域的所有续订通知和其他管理通知。

**WHOIS 隐私**

你可能已经注意到 ICANN 需要潜在的个人信息来注册域名。这可能会给注册人带来各种问题。有一种方法可以保护这些细节不被公众看到。它被称为 WHOIS 隐私，你可以为你的域名启用它，每年 5 美元。

![](img/291c59e98edd263fed87ea438808353f.png)

[https://whois.domaintools.com/](https://whois.domaintools.com/)

![](img/18c060872985150072047e80c7ee7c6c.png)

[https://www.whois.net/](https://www.whois.net/)

![](img/50c10119cffa967aaa599c3d13757ec0.png)

[https://lookup.icann.org/](https://lookup.icann.org/)

*   **公司及其收购**

你的目标公司在 6 个月前收购了另一家公司，所以现在那家下属公司也应该是你的目标。

我们可以先在新闻中找到一家公司的收购。每当这样的购买发生时，首先看看新闻总是一个好主意。

我们可以在目标公司的维基百科页面上搜索，看看它的收购情况

![](img/7a94fc329759b242387ef4c1ac685f48.png)

你也可以使用 crunchbase 获得关于公司收购甚至公司本身的非常精确的信息

[https://www.crunchbase.com](https://www.crunchbase.com/search/acquisitions/field/organizations/num_acquisitions/tesla-motors)

![](img/8aab960ca794f739474fed3cdf7d7e34.png)![](img/716a011a25e90b2539342af20810f550.png)![](img/9f9be5b7431bb439a035dba42fcc1a1b.png)

Censys 也是一个很好的网站，可以找到更多关于你的目标的信息

![](img/325dea367b043bae2efca15a50ddf624.png)![](img/7f98c166b56325c0c18cfea1c55ceefa.png)

*   您可以使用 nslookup 工具来查找 DNS 查询，如 CNAME，MX，PTR 等记录。

![](img/f03f3f79bff1012e15b0a678a918cadd.png)![](img/c3b35fc77583900c5c21b3d37d0843f3.png)

nslookup 用于检查 MX 记录

*   你也可以在基特曼网站上查看 SPF 记录

![](img/ef0e15d98a888dec81227524ce86ec2c.png)

https://www.kitterman.com/spf/validate.html

![](img/67aa6ae7dd05c9a4ee45b09ccf54546e.png)

基特曼曾经检查过谷歌的 SPF 记录

*   您可以进行反向 DNS 查找

【https://mxtoolbox.com/ReverseLookup.aspx 

![](img/a9a54a2488ec2e008e92f06b0c807bb4.png)![](img/d91ad839daf3c9266886884b9abedfb3.png)

IP 140.82.114.4 的反向 DNS 查找

*   你还可以使用 haveibeenpwned 来检查世界各地任何被黑客攻击的数据库中的密码或电子邮件是否被黑客攻击或泄露

![](img/969c016a3c23ee5c96508be42ab1d144.png)

它检查输入的电子邮件是否在任何被黑客攻击的数据库中

![](img/9b3825b8990b755341041689e86e2a3b.png)

它检查输入的密码是否在世界范围内被黑客攻击的数据库中

![](img/a3f4e9f2818312878a480b8eeedccd83.png)

我输入密码“admin ”,它显示我们进入了全球范围内被黑客攻击的数据库。

**行业工具**

这是一个巨大的话题，我只是指出了写这篇博客时我脑海中的信息，但这只是它所包含的冰山一角。

所以现在我提供一些最适合 OSINT 的工具。

*   OSINT 框架

这个工具就是你可以称之为瑞士军刀的东西。

这个工具可以给你目标的大量信息。

 [## OSINT 框架

### (T) -表示必须在本地安装和运行的工具的链接(D) - Google Dork，了解更多信息:Google…

osintframework.com](https://osintframework.com/) ![](img/b6756fb562bc7662d3d954e347874900.png)

看看这个工具能给我们的目标提供的信息，我的意思是太多了。

![](img/da3597d852599d4bacfd0ef4e7ffb161.png)

你可以搜索你的目标用户名

![](img/1070a2c9abc4cafd0776cdcc8ff16f44.png)

这些大量的信息可以在你的目标 IP 上获得

![](img/101f838c68d3e300688dfa653f427082.png)

你可以挖掘你的目标组织的业务记录。玩得开心！！

![](img/33a489959659d19643665f6076d280f4.png)

查看你目标的社交媒体账户

这个工具值得一个完整的博客，所以让我们把工作留到以后。但是，不要犹豫，看看这个工具，你只能掌握这个工具，如果你出去它可以用这个工具做你的研究。只有这样做才能掌握它，把它放在你的武器库中。

*   马尔特戈

Maltego 是用于开源情报和取证的软件，由 Paterva 开发。Maltego 专注于提供一个转换库，用于从开源中发现数据，并以图形格式可视化该信息，适用于链接分析和数据挖掘。

![](img/aef49a9db4aa6c5e3acc6d68a17e3f0e.png)

Maltego 允许创建自定义实体，除了作为软件一部分的基本实体类型之外，还允许它表示任何类型的信息。该应用程序的基本重点是分析现实世界中人、群体、网页、域、网络、互联网基础设施之间的关系(社交网络和计算机网络节点),以及与 Twitter 和脸书等在线服务的关系。其数据来源包括 DNS 记录、whois 记录、搜索引擎、在线社交网络、各种 API 和各种元数据。

*   theHarvester

这个工具可以从不同的公共来源(搜索引擎、pgp 密钥服务器)收集电子邮件帐户、子域名、虚拟主机、开放端口/横幅和员工姓名

![](img/7589434c45e59996e96eabd19f95e1df.png)

*   **搜索代码**

[Searchcode](https://searchcode.com/) 是一种独特的搜索引擎，在免费源代码中寻找智慧。

开发人员可以使用 Searchcode 来识别与代码中敏感信息的可访问性相关的问题。

该搜索引擎的工作方式类似于谷歌，但它不是索引网络服务器，而是在运行的应用程序或开发中的应用程序的代码行之间寻找信息。

搜索结果可以帮助黑客识别用户名、漏洞或代码本身的缺陷。

Searchcode 在来自 GitHub、Bitbucket、Google Code、GitLab、CodePlex 等的代码库中查找代码。

你也可以过滤不同类型的语言。

例如，如果您只想查看 HTML 和 PHP，或者只想查看 JavaScript，您可以选中复选框，Searchcode 将过滤所有信息。

## 主要特点:

*   使用特殊字符搜索
*   按编程语言筛选
*   按存储库过滤
*   '在源代码中查找'

![](img/0a9343024f0313f3f5482b2223689b22.png)

*   **蜘蛛脚**

[蜘蛛脚](https://www.spiderfoot.net/)是一款开源的侦察工具。

它通常被称为具有最广泛 OSINT 集合的指纹。

该工具可以自动向 100 多个公共来源发送查询，并收集 IP 地址、域名、web 服务器、电子邮件地址等方面的情报。

软件是用 Python 写的。

从蜘蛛脚开始，指定目标并在数百个不同的指纹模块中进行选择。

SpiderFoot 模块的例子可以是“sfp_arin.py ”,它向 arin 注册中心查询联系信息，或者是“sfp_crt.py ”,它从 crt.sh 中的历史证书收集主机名

一旦你选择了模块，蜘蛛脚就会自动收集信息并生成报告。

SpiderFoot 适用于 Windows 和 Linux。

![](img/46a7dfb43ef014e6f6a175689f72d148.png)

*   建筑

[build with](https://builtwith.com/)是一种很酷的方式，可以检测互联网上的任何网站使用了哪些技术。

它包括关于 CMS 的完整详细信息，如 Wordpress、Joomla、Drupal 等，以及完整深度的 Javascript 和 CSS 库，如 jquery、bootstrap/foundation、外部字体、web 服务器类型(Nginx、Apache、IIS 等)、SSL 提供商以及所使用的 web 托管提供商。

BuiltWith 还可以让您找到哪些是目前最流行的技术，或者哪些正在成为趋势。

毫无疑问，它是一个非常好的开源智能工具，可以收集关于任何网站的所有可能的技术细节。

![](img/05448e028185caf57129233567dc212a.png)![](img/0ed3e7c29612c4cca0257959a6cfd574.png)

使用 BuiltWith 查找 github.com 使用的技术

*   **侦察**

Recon-ng 是一个用 Python 编写的全功能网络侦察框架。通过独立模块、数据库交互、内置便利功能、交互式帮助和命令完成，Recon-ng 提供了一个强大的环境，在该环境中可以快速彻底地进行开源的基于 web 的侦察。

![](img/3df3a54f775c753b7563e7dafaf66e1d.png)![](img/2e151066ee17117c693ac47f534cf336.png)

*   **令人毛骨悚然的**

[Creepy](https://www.geocreepy.com/) 是信息安全专业人士的地理定位工具。它能够通过查询 Twitter、Flickr、脸书等社交网络平台，从任何个人那里获得完整的地理位置数据。

如果任何人上传一张图片到任何一个激活了地理定位功能的社交网络，那么你将能够看到这个人去过的所有活跃的 mal。

您将能够根据确切的位置，甚至是日期进行过滤。之后，您可以导出 CSV 或 KML 格式的结果。

![](img/160280e4bea3b416e0ce0fd4b0500cbd.png)

这个博客到此结束，但并不有趣。在博客的第二部分，我们将解决一些与 OSINT 相关的挑战。

欲知更多乐趣，请查看第 2 部分[https://medium . com/@ cyber defecters/let-go-deep-into-osint-part-2-b 9073 be 60 a0d](https://medium.com/@cyberdefecers/lets-go-deep-into-osint-part-2-b9073be60a0d)

作者- Infosec_boy

[](https://www.linkedin.com/in/anubhav-mandarwal-318952186/) [## anubhav mandarwal - CTF 玩家和博客作者-网络叛逃者| LinkedIn

### 查看 anubhav mandarwal 在世界上最大的职业社区 LinkedIn 上的个人资料。anubhav 有 4 份工作列在…

www.linkedin.com](https://www.linkedin.com/in/anubhav-mandarwal-318952186/) [](https://twitter.com/infosec_boy) [## 阿努巴夫·曼德瓦尔

### anubhav mandarwal 的最新推文(@infosec_boy)。💻渗透测试、bug 搜索、信息…

twitter.com](https://twitter.com/infosec_boy)