# mfa 旁路私有程序，abdulsec 方式

> 原文：<https://infosecwriteups.com/mfa-bypass-in-private-program-the-abdulsec-way-f677fea209f7?source=collection_archive---------0----------------------->

![](img/c8decbe6ed6149b3da64dfea05053403.png)

图片由 rezo 提供

嗨，我希望你做得很好，自从我上次写文章以来已经有一段时间了，今天我想和你分享一个我用来在私人程序中绕过多因素认证的技术

**使用的工具:**

burp 社区和 firefox

**第一次**
当您登录您的帐户并启用双因素身份验证时，注销
如果您下次登录，您将重定向至设置双因素身份验证

**第二个**
当你登录到一个已经完全设置了 mfa 的帐户时，你将被重定向到挑战页面，在那里必须输入一个由(6)个数字组成的有效代码，不要想使用暴力，因为它不是基于短信的 mfa

在我分析了具有双因素身份验证的帐户和启用了 2fa 但没有设置它的帐户之间的不同响应后，我发现不同之处仅在于响应头
X-Mfa-Redirect:mfaChallengePage 和 X-Mfa-Redirect: mfaSetupPage

为了绕过 2fa，我修改了响应头 X-Mfa-Redirect:以重定向到 mfaSetupPage 而不是 mfaChallengePage

# 复制步骤

1.  在 burp suite 中，进入代理>选项-匹配和替换
2.  添加响应头，匹配:mfaChallengePage 并替换:mfaSetupPage
3.  登录您的受害者帐户，该帐户具有双重身份验证
4.  您将重定向到设置新的 2fa
5.  完成设置 mfa，您将重定向到没有有效 mfa 代码的受害者帐户
6.  boom 你成功绕过了双因素认证

非常感谢你，

如果你喜欢这篇文章，在推特上关注我，你也可以给我买杯咖啡

[](https://twitter.com/moodiAbdoul) [## JavaScript 不可用。

### 编辑描述

twitter.com](https://twitter.com/moodiAbdoul) 

[https://www.buymeacoffee.com/abdulsec](https://www.buymeacoffee.com/abdulsec)

时间表

提交时间:世界协调时 2021 年 1 0 月 16 日 01 时 39 分 34 秒

奖励:600 美元世界协调时 2021 年 10 月 19 日 13 时 53 分 15 秒

*来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。* [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*