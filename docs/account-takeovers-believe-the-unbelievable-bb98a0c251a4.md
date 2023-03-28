# 账户接管——相信难以置信的事情

> 原文：<https://infosecwriteups.com/account-takeovers-believe-the-unbelievable-bb98a0c251a4?source=collection_archive---------0----------------------->

我为自己设定了一个目标，在一段时间内只寻找帐户接管问题。幸运的是，我确实完成了我的目标，报道了很多 ATOs。在这个博客里，我会分享几个有趣的。从表面上看，它们可能是很容易的发现。然而，我不得不在侦察上投入大量的时间。大多数目标只允许未经验证的测试。

*   **会话预测**

这个发现与我之前写的关于帐户接管的文章有关，你只有在阅读了之前的文章后才能明白这一点，这里([https://blog . niksthehacker . com/un authenticated-account-take-over-forget-password-c 120 B4 c 1141d](https://blog.niksthehacker.com/unauthenticated-account-takeover-through-forget-password-c120b4c1141d)

因此，在使用凭证登录到应用程序后，我在寻找另一种接管帐户的方法。我决定看看会话是如何创建的。为此，我查看了我的 burp 历史，发现它为每次登录创建了 hd_sessionID 值。该值本身是 4 位整数，例如 5000(什么？😳)，这里还要添加一点，每次成功登录，该值增加 1。假设当前会话 id 值为 5000，那么下次登录时，session id 值将为 5001。

要利用这个问题，您只需要 D** Ids(请参考上一篇文章)并强力攻击 sessionIDs。

因此，我接受了入侵者的请求，并将有效负载位置设置为 hd_sessionID。

![](img/c936cd6d7367a0dd27ecac0a7726e4b0.png)

您必须将一个 hd_userId 固定为您想要接管的帐户的 D** Id，并运行入侵者。

![](img/507d46172cb22606c0ff4d1d7895cbcd.png)

“5067”是该用户的正确 sessionID。出于显而易见的原因，我没有隐藏多少细节。

*   **证件灾难**

我正在寻找一个程序，其中有将近 700 个 bug 已经被接受并运行了 4 年多。因为这个程序处于额外激励模式(称为 blitz ),这促使我在上面寻找 bug。

因为该计划在通配符范围内，并且该公司处理教科书租赁。我决定把重点放在主应用程序(www.target.com)上，因为没有太多关于它的报道。它运行的是 WordPress。通过提供/wp-admin/，我转移到了 WordPress admin。

现在，我首先要解决的是有效的用户名。我使用了忘记密码，并想出了一个“测试”用户名。此外，我尝试了几个密码，其中一个密码“testtest”有效，我可以访问 admin。此外，RCE 很容易实现，但有一个停止和报告规则，而不是进一步升级。此外，他们使用 WordPress 进行其他多种产品和服务，这些凭证对所有人都有效。

![](img/6504abc63c34e4882ebed6f746761d93.png)

因为它是 cvss 10，我得到了 5000 美元的额外奖金。

![](img/4ae39be145917a6958fa15310f1f832b.png)

*   **CVE-2018–9845**

因此，在寻找相同的(上图)应用程序时，我偶然发现了一个现场会议，导师可以教学生，这是一个付费的功能(按需付费)，但客户希望更多地关注这一领域，这似乎是该应用程序的核心部分。

![](img/708b794ea6235fb362b092005bc438ab.png)

在文本编辑器上快速检查元素时，我发现它正在运行以太网键盘([https://github.com/ether/etherpad-lite](https://github.com/ether/etherpad-lite))。快速的谷歌搜索帮助我发现它容易受到几个简历的攻击。我发现这是一个有趣的 CVE-2018–9845，“1.6.4 之前的 Etherpad Lite 可用于管理员访问。”但是我找不到任何相关的漏洞，所以我继续在这里查看了 commit([https://github . com/ether/ether pad-lite/commit/FFE 24 c 3d d 93 EFC 73 e 0 CBF 924 db 9 a 0 cc 40 be 9511 b](https://github.com/ether/etherpad-lite/commit/ffe24c3dd93efc73e0cbf924db9a0cc40be9511b)

![](img/ea89e52b6a43693d932a619b20e1ee79.png)

等等什么？

在浏览到/path/path2//ec2/admin/时，我得到以下提示，要求我输入用户名和密码。

![](img/af577d54eddd88e8713f0f9c7a138c66.png)

对于旁路，我只需要大写`Admin`并获得管理员访问 LOL。

![](img/b03cfc8c69a0f6a1923a7499253e7750.png)

*   **内测答案**

这是联邦计划之一(另一个旧计划和通配符)。我做了侦查，发现了一个子域狩猎(另一个登录/忘记通行证)。在尝试任何事情之前，我想收集有效的用户名。我遵循密码重置过程，因此，正如所料，应用程序首先会要求输入用户名。首先，运行一个入侵者来找到有效的用户名，我通常使用这个单词列表([https://github . com/danielmiessler/sec lists/blob/master/Usernames/Names/Names . txt](https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt))作为用户名。

![](img/98e501045d4701eb690f52dc2d82f4fe.png)

现在，我有很多有效的用户名，有效用户名的下一个屏幕是输入答案。现在，每个用户名都有自己的安全问题，但我重点关注具有以下安全答案的用户名

“你配偶父亲的名字是什么”为什么？因为我得到了另一个单词表来暴力破解它([https://github . com/danielmiessler/sec lists/blob/master/Usernames/Names/malenames-USA-top 1000 . txt](https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/malenames-usa-top1000.txt))

好的，接下来我用名字用户列表对安全答案进行另一次入侵攻击。

![](img/efb5b68a785719563303e51f85ddae01.png)

我从名单上找到了正确的名字。此外，您只需要为用户设置新的密码和安全问题，并接管他们的帐户。

![](img/53de813e21b827f5f295d4e8f9af5b27.png)

如果有人为用户的帐户设置了新的安全问题，此问题也可能会阻止用户重置其密码。呀！。Imperva WAF 的实现是为了防止任何滥用，但有趣的是，它会在 1K 次尝试后启动，所以对我来说这从来都不是问题。

*   **忘记密码？我给你换一下**

如今，我能够相当频繁地发现这类问题。这是联邦项目的另一个子域的情况。又一个应用，又一个“登录/忘记密码”。

我经历了忘记密码和输入用户名，它给出了一个有趣的错误有关的电子邮件，(我忘了它是什么)。我在它的用户名列表上运行入侵者，我实际上很震惊，它公开了有效用户名的电子邮件地址(为了隐私，我隐藏了电子邮件)

![](img/f6079d4c9594627b86d187fc4a4cce87.png)

有意思，但我们还可以做些什么。我想在这里搜索用户被破解的密码。所以我很快浏览了一个有大量被破解密码的应用程序(感谢**帕思·马尔霍特拉**推荐了一个)，当我搜索时，我只找到了三个结果，其中只有一个结果返回了明文密码。

![](img/f7e72d25bddb46eda4004c14036bab78.png)

有意思，我在传送门尝试了这个用户名和密码，登录了 LOL。

![](img/e09b85fa37b736717511cb7b473415b3.png)

类似地，我们可以接管这个应用程序上的任何帐户，如果他们的密码被泄露，并且他们在门户中重复使用相同的密码。我发现了很多。

*   **弱密码？2021?**

在侦察同一个目标时，我发现了一个运行 mailman([https://www.gnu.org/software/mailman/](https://www.gnu.org/software/mailman/))的子域

它有几个列表，但在通过/mailman/admin/{listname}时，它要求输入密码:

![](img/a89de250ef5aeee1d9c88bfed9866d68.png)

当输入“密码”并点击“让我进去”时，我就可以进入管理员账户了，哈哈！。这不仅仅是一个名单的情况，还有 3-4 个其他人有相同的密码。

![](img/ffc4453c058b21b7608da7c6df22551f.png)

*   还有更多……

在同一个目标上，我发现了另一个子域，应用程序在那里为某种类型的报告注册用户。我很快用谷歌搜索了一下:

站点:subdomain.com

发现了一些有趣的结果:

![](img/4769bd192047009a7b353f1e91efe31b.png)

有趣吧？(PII)。我点击了链接，应用程序显示了以下信息

![](img/540357dd1e8bffd263f54474067aaa8c.png)

因此，所有的用户注册表单信息仍然可以在网站上通过一个独特的链接访问，而 PII 是公开的，但对我来说有趣的部分是密码描述，这意味着用作密码提示。我发现用户在这个密码描述字段中分享了他们的密码，密码以明文形式存储在网站上。为了证实这一点，我在其中一个账户上做了凭证填充，结果成功了。

暂时就这样了。下一篇文章再见。保持好奇。

页（page 的缩写）如果你想成为 synack 红队的一员，请在 twitter (@niksthehacker)上给我发你的简介，如果你有成为 SRT 的素质，我会推荐你的。

感谢[巴武克·贾恩](https://medium.com/u/504351aeb258?source=post_page-----bb98a0c251a4--------------------------------)和[坎纳特·卡迈勒](https://medium.com/u/af3671776a72?source=post_page-----bb98a0c251a4--------------------------------)的校对。