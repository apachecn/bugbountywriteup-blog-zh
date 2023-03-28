# 谷歌黑客/多金

> 原文：<https://infosecwriteups.com/google-hacking-dorking-3a58757a9ae7?source=collection_archive---------0----------------------->

![](img/2c83e5c4dad99ae4d3d485b8d9852596.png)

## 什么是呆瓜？

Google hacking 或 Dorking 只不过是一种寻找更专业的东西的方式，通过“Google Hacking”这个名称，你可能会给人一种只在 Google 中使用的印象，但这是不正确的。Dorking 只不过是一种高级搜索，我们使用操作符作为过滤器将搜索直接导向我们想要的地方，我们还使用符号来搜索精确的单词或短语。这将帮助我们搜索几乎任何我们在互联网上找到的搜索引擎。

## 重要

了解一点“黑客”对我们生活的许多方面都有帮助，它不仅仅是搜索易受攻击的页面，记住“黑客”这个术语首先用于当一个人精通一件事情，以至于他可以从其他角度看到事情，从而看到或实现其他人不能看到的事情，如果我们留下一点计算机科学，我们可以使用一个例子。
一个多年从事汽车工作、专门研究发动机的机械师，随着时间的推移，他已经比任何其他机械师更了解发动机的问题，在这方面有更多的经验，可以说他是一个了解这一点的机械“黑客”。
回到多金的话题，所有人都知道一点这一点是非常重要的，在这篇文章中，我们将尽可能详细地解释如何开始，如果你已经知道一些这方面的知识，你可以加强基本话题。

## 基本概念

如前所述，这些高级搜索使用运算符和符号，如下所示:

让我们从一些非常常见的运算符开始:

*   站点:它只在网站中搜索“站点:”后面的结果
*   这个搜索将只向我们显示来自谷歌页面的结果
*   将只搜索标题
*   `Ejemplo: intitle:"Google dorking"` 此搜索将只显示标题中包含短语“google dorkin”的文章
*   `Ejemplo: inurl: /parametro.php?id=`这个搜索将显示包含“/parameter o . PHP”的页面结果。id= "在他们的 url 中。

注意:这在开始时可能会与“站点”操作符有点混淆，因为如果我们没有很好地表达我们想要查找的内容，并且我们放置了一个网站，它将向我们显示与“站点”操作符相同的结果。请记住，根据页面管理员是否将链接配置为更加友好，链接或 url 具有以下语法。

带有不友好链接的网站:

```
https://[www.examplewebsite.com/parametro1.php](http://www.examplewebsite.com/parametro1.php)?Id=1&parametro2.php?Id=22
```

带有友好链接的网站:

```
https://[www.examplewebsite.com](http://www.examplewebsite.com)/example
```

*   文件类型 o ext:只搜索(doc，xls，txt…)
*   这个操作符伴随着我们刚才看到的那些中的一个。示例:

`site:www.paginaweb.com filetype:.pdf`

这次搜索将向我们展示。“松散”的 pdf 文件或从网页[www.paginaweb.com](http://www.paginaweb.com)中过滤出来的 pdf 文件

*   有关系的
*   它会向我们展示类似于谷歌的页面，也就是说，它会向我们展示其他搜索引擎
*   缓存:显示网页在 Google 缓存中的结果。
*   `Ejemplo: cache:www.google.com` 它将显示保存在 google cachedefine 中的网页:busca definiciones de palablas
*   `Ejemplo: define:linux` 它会向我们显示定义为 linux 的结果
*   我们将寻找我们输入的精确短语
*   它会显示字母表所在的页面
*   天气:
*   `Ejemplo: weather:México`
*   股票:它将向我们展示财务信息
*   这将向我们展示股票市场的金融信息
*   链接:它向我们展示了我们正在寻找的页面所链接的页面
*   它将向我们显示所有链接到你的页面上的任何 youtube 链接的页面

现在我们将看到一些非常重要的符号:

*   " "(comillas):搜索精确短语
*   在这种情况下，我在寻找我自己的昵称或我的 twitter 昵称
*   `+ y -`:包含或排除任何单词
*   这给了我们很多谈论动物的网页，但试图排除所有谈论狗的网页
*   `*`:通配符，任意单词，但只能一个单词。
*   `Ejemplo: site:*.google.com` 这将显示所有的谷歌页面，包括他们的子域。

# 如何在“黑客”中使用这个？

首先，我们必须非常清楚我们想要实现什么，我们要寻找什么，我们想要找到什么。

为了启动 Google hacking，从我们希望找到并非每个人都能找到的“信息”的角度来看，我们必须知道包含这些信息的文件，我们必须知道网页所基于的系统，我们必须对所有事情都有所了解。

我们将要看到的第一个例子是如何找到文件“robots.txt ”,但首先要找到这个文件，我们必须知道:它是什么？它包含什么？为什么页面使用它？在不知道是什么的情况下，仅仅通过寻找来寻找某样东西。

“robots.txt”文件是为了方便网站的索引。该文件用于指导机器人应该和不应该抓取什么内容以及应该如何抓取。更简单地说，这个文件是用来使网页“安全”免受谷歌搜索的，因为在文件中出现了管理员不希望在谷歌结果中看到的页面，来吧，这就是如何排除那些找不到的页面。

这个特定的文件是这样写的:

```
User-agent: *
Disallow: /example/
Disallow: /1/example/
Disallow: /2/
Disallow: /example2/
Disallow: /example3/
Disallow: /3/example4
Disallow: /3333
Allow: /i_want_to_be_visited
Allow: /i_want_it_too
```

它在页面链接中的使用方式如下:

`[https://www.paginadeejemplo.com/robots.txt](https://www.paginadeejemplo.com/robots.txt)`

用它我们有很多方法可以找到它，例如…我们知道它在链接的哪个部分，我们知道它用在 url 中:

我们可以进行如下搜索:

`inurl:robots.txt`但这将向我们显示非常一般的结果，它可能会找到文件或与之相关的内容

这是我们实际使用操作符的时候，我们将混合使用它们以获得最佳结果:usando el operador "site" + "inurl "我们进行更具体的搜索:

`site:www.google.com inurl:robots.txt`这样我们就可以在谷歌页面上进行搜索，瞧！给我们的结果是:【https://www.google.com/robots.txt 的 

但是如果我们想使搜索更加具体，我们可以使用:

`site:*.com.mx inurl:/robots.txt intext:"User-agent:"`

在这种情况下，我们将结果减少到只有具有。com 域以及。mx ".com.mx "这意味着我们正在寻找墨西哥网页，因为。mx 域属于墨西哥托管的页面，当找到这些页面时，应用运算符 inurl: /robots.txt，将找到的更多页面减少到仅在其 url 中包含单词“robots.txt”的页面，最后输入 intext:“User-agent:”以进一步减少之前的结果，并验证之前找到的页面中是否有单词“User-agent:”在其中，现在我们可以确保找到的页面将显示每个页面的 robots.txt 文件。

这只是如何使用操作符找到酷东西的一个例子。

另一方面，我们也可以专注于寻找在特定 CMS 中制作的页面，例如 WORDPRESS，它的特点是工作起来非常舒适，非常容易，因为它包含许多非常有用的插件，但同时这些插件也会有许多缺陷，允许其他人利用各种漏洞。

要找到在 WORDPRESS 中创建的页面，首先我们必须对它有所了解，它的文件和文件夹的基本结构，最重要的文件…等等，在这里我将试着解释一下，以便更好地理解为什么知道这些是重要的:
WORDPRESS 在它的 url 中使用了下面的结构:

`https://tudominio/wp-admin o https://tudominio/wordpress/wp-admin o [https://tudominio/wp/wp-admin](https://tudominio/wp/wp-admin)`

重要的文件夹和文件:

```
* wp-admin: Folder where the WordPress back-end files are saved, this part of the installation is never modified. * Wp-content: It is the folder where all the content is saved in file format (not database)* wp-includes: It is a folder of files that WordPress needs to work, its API and the main libraries are in this folder that is never modified. * Index.php: it is the main file that is accessed and from where the rest of the parts are loaded of WordPress.* wp-config-sample.php: This file is the template of what will finally be the WP-CONFIG.PHP file after the installation of the CMS * xmlrpc.php: It is a file that offers communication through the XMLRPC.PHP protocol, currently WordPress receives many attacks through of this file, so it is important to emphasize its safety and protection.* wp-login.php:  It is a very important file, since it is the one that is in charge of managing the login of users, both normal users and administrators.
```

现在，记住这一点，我们已经知道如何使用 dorking 找到在 wordpress 中创建的页面:

我们知道 wordpress 是如何创建它的 url 的，为了找到页面，我们可以使用下面的方法:

`inurl:/wordpress/wp-content` `inurl:/wordpress/wp-admin` `inurl:/wordpress/wp-includes`

在这里，我们使用 inurl 在 wordpress 目录中直接寻找一个文件夹，这将向我们显示许多页面，在这些页面中我们将直接访问(如果我们有权限的话)wordpress 文件。

# 傻瓜的 Ejemplos de dorks

在这一部分中，我将使用我自己的 twitter 账户中的一个帖子作为补充，在那里我发表了许多我觉得有趣的傻瓜，我们将在这里分析其中的一些:

我们将使用的第一个选项是:

inurl:wp-config.php intext:DB _ PASSWORD-stack overflow-WP 初学者-论坛-论坛-主题-博客-关于-文档-文章

这个傻瓜允许我们找到在 wordpress 中创建的页面，我们专门寻找 wp-config.php 文件，然后我们使用 intext: DB_PASSWORD 过滤找到该单词的结果，这是为了找到数据库配置，我们找到诸如:用户、密码、数据库名称等非常重要的信息，这些信息不应该对任何人可见，最后我们有带“-”的单词，这意味着我们从我们的搜索中排除这些单词。

另一个非常有趣的呆子是:

标题:“索引”正文:“iCloud 照片”或正文:“我的照片流”或正文:“相机胶卷”

在这个例子中，我们使用 intitle:“index of”来查找网页中的目录，这意味着我们正在查找包含目录列表的网页，通常这些目录是不可见的，然后我们在几个 intext 和 or 之间过滤结果，这意味着我们正在查找这 3 个短语中的任何一个。

在这种情况下，我们要寻找易受攻击的 iCloud 设备，通过这些设备，我们可以看到所有正在共享的照片。这个呆子可以被修改来寻找不同的东西。

但是，我们不仅使用这些呆瓜来查找文件夹或文件，我们还可以找到运行一项服务的服务器:

intitle:“欢迎使用 JBoss”inurl:“8080/JMX-console”

有了这些呆瓜，我们可以找到运行 Jboss 系统的服务器。

# sql 和 xss 注入的呆子

Dorking 与 sql 和 xss 注入密切相关，因为许多黑客利用 dorks 寻找易受攻击的页面。如果您想更深入地了解 sql 注入，我建议您通读一下我撰写的这篇文章，重点是:

[https://github . com/Y000o/SQL _ injection _ basic/blob/master/SQL _ injection _ basic . MD](https://github.com/Y000o/sql_injection_basic/blob/master/sql_injection_basic.md)

在这篇文章中，我试图用非常详细的方式解释 sql 注入的基础，无论是手动还是使用工具自动进行。
回到主题，利用一些傻瓜使搜索更容易找到易受 sql 和 xss 注入攻击的页面，有些是:

```
specials.cfm?id=
store_listing.cfm?id=
store.cfm?id=
store_bycat.cfm?id=
storefront.cfm?id=
Store_ViewProducts.cfm?Cat=
store-details.cfm?id=
StoreRedirect.cfm?ID=
storefronts.cfm?title=
storeitem.cfm?item=
subcategories.cfm?id=
tuangou.cfm?bookid=

tek9.cfm?
template.cfm?Action=Item&pid=
topic.cfm?ID=
type.cfm?iType=
view_cart.cfm?title=

updatebasket.cfm?bookid=
updates.cfm?ID=
view.cfm?cid=
view_detail.cfm?ID=
viewitem.cfm?recor=

viewcart.cfm?CartId=
viewCart.cfm?userID=
viewCat_h.cfm?idCategory=
viewevent.cfm?EventID=
WsAncillary.cfm?ID=

viewPrd.cfm?idcategory=
ViewProduct.cfm?misc=
voteList.cfm?item_ID=
whatsnew.cfm?idCategory=
WsPages.cfm?ID=HP
inurl:".php?cid="+intext:"online+betting"

inurl:".php?cat="+intext:"Paypal"+site:UK
inurl:".php?cat="+intext:"/Buy Now/"+site:.net
inurl:".php?id=" intext:"View cart"
inurl:".php?id=" intext:"/store/"

inurl:".php?id=" intext:"Buy Now"
inurl:".php?id=" intext:"add to cart"
inurl:".php?id=" intext:"shopping"
inurl:".php?id=" intext:"boutique"
inurl:".php?cid=" intext:"Buy Now"

inurl:".php?id=" intext:"/shop/"
inurl:".php?id=" intext:"toys"
inurl:".php?cid="
inurl:".php?cid=" intext:"shopping"
inurl:".php?cid=" intext:"add to cart"
inurl:".php?cat="

inurl:".php?cid=" intext:"View cart"
inurl:".php?cid=" intext:"boutique"
inurl:".php?cid=" intext:"/store/"
inurl:".php?cid=" intext:"/shop/"
inurl:".php?cid=" intext:"Toys"
inurl:".php?cat=" intext:"/store/"
```

# 呆子去找相机

我们也可以找到我们可以进入的摄像机

```
inurl:/sample/LvAppl/lvappl.htm
allinurl:control/multiview
intitle:”Live View / – AXIS"
```

# OSINT y 呆子

使用 Dorks，我们还可以关注 OSINT，这是一套收集公共信息的技术和工具。

要搜索姓名:

```
“nombre” site:página web“nombre” site:http://instagram.com
```

用户名:

```
inurl:username site:páginaallinurl:username site:página
```

搜索网页信息，获取与其相关的页面及其所有子域:

```
Búsquedas en una página web:
site:http://example.comPáginas parecidas:
related:http://example.com Subdominios:
site:*.example.com -www
```

电子邮件

```
These will help us collect emails from different web pages:“@example.com” site:site.com “email” site:site.com filetype:csv | filetype:xls | filetype:xlsx site:http://example.com intext:@gmail.com filetype:xls 
```

额外由@pedr4uz 提供:

`[https://twitter.com/pedr4uz?s=20](https://twitter.com/pedr4uz?s=20)`

```
"person_name" intext:cpf AND filetype:pdfderivado -> "person_name" intitle:aprovado + intext:cpf AND filetype:X
```

# 最终重要性

正如我们在这篇文章中看到的，了解多金打开了一个可能性的世界，在这个世界里，我们更容易找到其他人不容易找到的信息，因为我们很好地理解了专业搜索的使用，我们有很大的权力，这就是为什么它是好的，我想非常明确地说，我们可以找到的许多信息可能是受保护的，因此如果我们未经许可访问，我们可能会陷入麻烦。本文所学的一切纯粹是为了教育目的，他们想给什么用是每个人的决定。