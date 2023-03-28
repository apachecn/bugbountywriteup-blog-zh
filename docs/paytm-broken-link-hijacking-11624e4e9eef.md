# pay TM-断开的链接劫持

> 原文：<https://infosecwriteups.com/paytm-broken-link-hijacking-11624e4e9eef?source=collection_archive---------1----------------------->

![](img/e2dc27decb15911a5c12d86b5bb8e344.png)

大家好…

**Lohith** 此处，**(资深安全工程师** & **伦理黑客**出自 **Bengaluru** )。我希望你们都过得很好。

这篇文章是关于 **Paytm 断链劫持**漏洞的。

B 更多信息👉[](https://www.indusface.com/blog/what-is-broken-link-hijacking/#:~:text=Broken%20Link%20Hijacking%20(BLH)%20is,resources%20from%20external%20URLs%2F%20points.)****。****

**该域具有正常的登录功能。通常，我会寻找速率限制、密码令牌问题等等。所以我开始寻找速率限制和密码令牌相关的问题，但是没有运气。**

**过了一段时间，我在手机上查看邮件。所以那一次，我只是打开了这个忘记密码的邮件。通常我会检查邮件模板上的所有链接，像**社交媒体**。**

**在这里我发现了 3 个社交媒体链接:**脸书**和**推特**。**

**![](img/682e50b06725b21dce23bbed5c2db6d7.png)**

****密码重置邮件模板****

**我只是打开了脸书的链接，它把我重定向到了他们的脸书官方页面。这里没有问题。然后我打开第二个 Twitter 链接，那个链接就断了。它被重定向到一个无效的 **Paytm 博客**页面。但是，在这种情况下没有影响。因为该域名仅由 **Paytm** 所有。**

**有趣的部分来了…**

**然后我打开第三个 twitter 链接，它重定向到。…….😁**

**![](img/3c2a7176cbb81088c009798afc908d06.png)**

****断开的 Twitter 链接****

**是啊。它会重定向到 Twitter 错误页面(“此帐户不存在”)。这意味着没有使用该用户名的用户帐户。**

****黑客模式开启🎭…****

**很快，我创建了一个假的 Twitter 账户，并把我的用户名改成了这个用户名。**

**![](img/d8d039898093ecb22e690ed8a369928f.png)**

****用户名更新****

****最后一部分……****

**每当 Paytm 用户请求一个*忘记密码*，如果他点击电子邮件模板上的 Twitter 链接，他将被重定向到这个账户。**

**![](img/edad94a89ac111a76fb11c6a50e20fc9.png)**

****报告详情:****

*   ****2021 年 12 月 24 日下午 08:14**—向 Paytm 安全团队报告。**
*   ****2022 年 1 月 03 日下午 02:44**—pay TM 安全团队的首次回应。**
*   ****2022 年 1 月 13 日下午 06:47**—pay TM 安全团队修复了该问题。**
*   ****2022 年 1 月 13 日 07:24 PM** —重新测试&确认修复**
*   ****2022 年 1 月 19 日上午 9:53—**颁发 [**赞赏证书**](https://twitter.com/lohigowda_in/status/1483709274199310336?s=20)**

****感谢阅读！….黑客快乐！****

> *****Linkedin:****[*Lohith Gowda M*](https://www.linkedin.com/in/lohigowda/)***
> 
> ******碎碎念:****[*lohigowda _ in*](https://twitter.com/lohigowda_in)****
> 
> *******insta gram:****[*lohigowda . in*](https://www.instagram.com/lohigowda.in/)*****
> 
> *******作品集:【https://www.lohigowda.in/】****[](https://www.lohigowda.in/)*****

*****[![](img/4bc5de35955c00939383a18fb66b41d8.png)](https://www.buymeacoffee.com/lohigowda)*****

# *****🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。查看更多详情并在此注册。*****

*****[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)*****