# 您需要的所有工具:识别

> 原文：<https://infosecwriteups.com/every-tool-you-need-for-reckoning-47f20d3828ed?source=collection_archive---------3----------------------->

![](img/ba80e4b7735ca187077b69557e0c8646.png)

**嘿，网络朋克们，**我希望你们都做得很好，**如果没有**那么我在这里**消除你们所有的困惑****选择哪个工具**来**收集关于你们目标的信息**。我看到很多有抱负的黑客忘记了，或者我应该说忽视了**侦察**的力量。他们在黑客阶段通常做的事情，无论是 bug 奖励还是为组织测试，他们都直接跳到目标上。

但是我想，我们都记得亚伯拉罕·林肯的那句名言

> 如果我有 8 个小时去砍树。我会花 6 个小时磨我的斧子。

![](img/37d4fddd94e7932487a7eb609e645160.png)

那么，在攻击你的目标之前收集信息是否重要。在今天的文章中，我们将**列出你可以用于特定目的的工具**，以**使你的开发更加容易**。但是，我们也知道我们永远不能忽视手动识别的力量。从现在起，我们将完全专注于自动化，以节省我们的时间和精力。现在，不要浪费时间，让我们直接进入正题。

**注意:**这将是**直截了当的**，因为我将**列出您需要的工具**并进行简单描述。其余的，可以**点击工具名称**了解更多。您可以**将这篇文章添加为书签**。每当你测试目标时，这将是你的**转到注释**。它会确保你不会错过任何一个端点。

# 1.寻找目标的子域

*   [**Amas**](https://github.com/OWASP/Amass)**s**——这是你能要求枚举你的目标的子域的最好情况。它使用不同的技术为你收集信息。更多信息，点击 [**此处**](https://danielmiessler.com/study/amass/) 。
*   [**敲**](https://github.com/guelfoweb/knock)——这是另一个用 python 语言写的野兽，你可以用它来进行 OSINT。更多信息，点击 [**此处**](https://github.com/guelfoweb/knock) 。
*   [**sublist 3r**](https://github.com/aboul3la/Sublist3r)——另一个用 python 写的快速扫描仪。更多信息，点击 [**此处**](https://www.geeksforgeeks.org/what-is-sublist3r-and-how-to-use-it/) 。

这三个工具足够你交叉验证，如果其中任何一个向你显示假阳性结果。

# 2.用于查找任何敏感信息/信息泄漏/密码的 API 密钥

对此的一个词回答是 [**Github**](https://github.com/) 本身。运用你的创造力和搜索技巧来充分利用它。如果你想了解搜索技巧，点击 [**这里**](https://github.com/techgaun/github-dorks) 。但如我所承诺的，我会告诉你最好的工具，&工具是 [**GitDorker**](https://github.com/obheda12/GitDorker) 。

# 3.检查端口上的公共 IP 暴露

*   [**庄丹**](https://addons.mozilla.org/en-US/firefox/addon/shodan_io/)——这大概是为了**列举公共 IP 的**而做的最好的。此外，您可以将此添加为您的扩展。查看有关该工具的更多信息。在这里点击[](https://www.shodan.io/)**。**

# **4.谷歌多金**

*   **[**谷歌黑客数据库**](https://www.exploit-db.com/google-hacking-database) :公司发布他们的呆子很久了。不要忘记使用这个强大的工具来找出任何**敏感信息。****

# **5.对于端口扫描**

*   **[**Nmap**](https://github.com/nmap/nmap)**

****优点:-** 可以扫描 IP 和子域。**

****缺点:-** 速度慢。**

*   **[**质量扫描**](https://github.com/robertdavidgraham/masscan)**

****优点:-** 比 Nmap 快很多。**

****缺点:-** 只能扫描 IP。**

**选择哪一个现在是你的了。我个人比较喜欢这两个。😁**

# **6.对于目录暴力**

*   **[**目录搜索**](https://github.com/maurosoria/dirsearch)**
*   **[](https://github.com/OJ/gobuster)**
*   ****[ffuf](https://github.com/ffuf/ffuf)****
*   ****[](https://portswigger.net/burp/pro)****
*   ******[**wfuzz**](https://github.com/xmendez/wfuzz)******

****这些对你来说已经足够了。是的，你也可以使用**dirb(Kali 的内置工具)**来达到这个目的。但是，我个人更喜欢这些。****

****所有这些不同用途的工具将在你的黑客之旅中给你很大帮助，并使你的目标更容易理解。****

******在结束之前，**我很乐意分享我在我的 **Firefox** 中使用**的**扩展**列表，特别是为了**重新识别**以节省我的时间和尝试。******

# **必须有插件在火狐的信息收集**

**![](img/6616deadca27af3fb42d88e2d4a8db0d.png)**

*   **[**retire . js**](https://addons.mozilla.org/en-US/firefox/addon/retire-js/)——检出目标应用中过时的组件。**
*   **[](https://addons.mozilla.org/en-US/firefox/addon/wappalyzer/)****/**[**内置**](https://addons.mozilla.org/en-US/firefox/addon/builtwith/) -查看网站使用的技术。****
*   ****[](https://addons.mozilla.org/en-US/firefox/addon/shodan_io/)**——检查任何暴露的公共 IP。******
*   ******[**Cookie 管理器**](https://addons.mozilla.org/en-US/firefox/addon/edit-cookie/)**/[**editthisocookie 2**](https://addons.mozilla.org/en-US/firefox/addon/etc2/)**-**找出会话相关漏洞。********

******参考**-[https://the hackerish . com/bug-bounty-tools-from-enumeration-to-reporting](https://thehackerish.com/bug-bounty-tools-from-enumeration-to-reporting/)****

****那么，这就是这篇文章的内容，希望你喜欢。我会带着另一篇文章回来找你。在那之前，保重，T2 继续寻找。 **不断挖掘和学习新东西。😍******

****如果你喜欢这些内容，你可以在这里支持我:-**@**[**buymeacoffee.com/ethicalkaps**](http://buymeacoffee.com/ethicalkaps)✌****

****下一篇文章再见。在那之前珍惜你的生命。和平！🙌****

> *****你可以在*[***Twitter***](https://twitter.com/EthicalKaps)*上关注我，在*[***Spotify***](https://open.spotify.com/show/49AHAyFgIy7E2NDjuGRaMm?si=lVPL_DBGRkGIC8DzfTXNbw)**上收听我的评论，在*[***insta gram***](https://www.instagram.com/iam_kapilchoudhary/)*上关注我。******

## *****如果你喜欢这个故事，请点击👏想按多少次就按多少次，并分享来帮助其他人找到它！欢迎在下方留言评论。*****