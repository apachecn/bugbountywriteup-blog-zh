# 接管脸书页面标签

> 原文：<https://infosecwriteups.com/taking-over-facebook-page-tabs-4d794cbf2113?source=collection_archive---------1----------------------->

## 从脸书自己的脸书页面问好…

![](img/a41d7bb4eca4e1055edbef6ae8031d22.png)

照片由 [@Tim Bennett](https://unsplash.com/@timbennettcreative) 在 unsplash 拍摄

在这篇文章中，我将描述我是如何接管脸书自己页面上的 4 个标签的。

# 1.脸书印度大使

我像往常一样浏览脸书，没有心情测试任何东西。然后我访问了脸书印度的页面，查看是否有来自脸书印度的更新，这时我注意到一个页面标签**脸书印度大使**。我点击它查看脸书印度的品牌大使，标签显示了一个 heroku 错误页面。我惊讶地发现。我觉得很有趣，所以我决定进一步挖掘。我发现它在主要部分的 iframe 中加载了一个第三方网站。

网址是 http://immense-atoll-4159.herokuapp.com/[](http://immense-atoll-4159.herokuapp.com/)*，我直接访问了该网址，以验证该子域不存在。如果子域不存在，Heroku 会显示一个不存在的错误页面。*

*所以我登录了我的 Heroku 账户，创建了一个新项目，并给*巨大环礁-4159* 作为项目 id。然后我为 PoC 创建了一个简单的 NodeJS 脚本，并将其部署到 Heroku 应用程序中。*

*然后，我访问了印度脸书页面中的大使选项卡，并检查了框架源代码，以确认我的代码正在那里加载。*

# *2.脸书印度直播*

***Livestream** 是 Vimeo 拥有的 Livestream.com 账户在脸书页面的另一个标签。被框住的网址是*livestream.com/facebookindia/index.php*，我在标签中看到一个页面未找到的错误。*

*我访问了 livestream.com，在那里用用户名 *facebookindia* 创建了一个账户，但是由于 */index.php* ，我无法在那里显示内容。但是如果有人访问 livestream.com/facebookindia,，他们会看到我上传的视频。*

# *3.脸书葡萄牙直播*

*脸书葡萄牙页面有一个标签 **F8 |直播**框住了网址*livestream.com/f82011/index.php*但是用户名在直播中不存在。我可以通过 Vimeo 在 Livestream 中接管那个用户名。由于 url 中的*index.php*，我无法在脸书葡萄牙页面提供内容。*

# *4.脸书巴西资源*

*巴西脸书有一个名为 **Recursos** 的选项卡，它框住了 URL[*https://www.webuzzapps.com/webuzzapp/137331002992480/tab*](https://www.webuzzapps.com/webuzzapp/137331002992480/tab)，并显示一个错误。当我挖它的 dns，没有任何回报。然后我访问了 GoDaddy，检查域名*webuzzapps.com*是否可供出售，我看到该域名正在出售。这是一个高级域名，因此价格昂贵，但无论谁购买它，都可以在脸书巴西的页面上的标签中提供内容。*

# *附加服务*

*1.印度脸书有一个标签**故事**框住了网址*facebook.involver.com*而子域不存在。Involver 现在是 Oracle 的一部分。由于我不熟悉 oracle 服务，我不确定是否有可能接管这一工作。*

*2.印度脸书有一个标签**邦选举跟踪器**框住了不存在的网址*d6uon097akywu.cloudfront.net*。*

*3.脸书印度公司有一个标签 **#100 位女性**框住了网址*100 Women India . vote now . TV*而摘要结果是*

> *100 women India . vote now . TV . 59 wildcard.votenow.tv.edgekey.net CNAME。wildcard.votenow.tv.edgekey.net。e5223.g.akamaiedge.net CNAME 21599 号。【e5223.g.akamaiedge.net】T21。19 岁的 23.32.136.41*

*它指向 akamai，但不在那里。*

# *反应*

*脸书对这些报道进行了分类，删除了那些标签，并以信息丰富为由将其关闭，称没有潜在影响。*

*我告诉他们开放重定向是可能的，攻击者还可以在 iFrame 的上下文中提供 JavaScript(CSP 不适用于 iFrame 中的 JavaScript 代码)，并可以创建虚假的钓鱼页面和其他形式来说服访问者输入数据。*

*脸书回应说，“这是所有页面标签的固有风险:你可以把人们从脸书重定向到第三方网站。”*

*感谢阅读。*

**关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。**

*[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)*