# 我是如何绕过 showmax 的父母密码的

> 原文：<https://infosecwriteups.com/how-i-was-able-to-bypass-parental-pin-of-showmax-e6d6ec3af92d?source=collection_archive---------2----------------------->

Showmax 是一项流媒体服务，提供各种获奖的电视节目、电影、动漫、纪录片，

当我在推特上看到@ l[ordjerry 0x 01](https://twitter.com/lordjerry0x01)[https://hackerone.com/reports/1077520](https://hackerone.com/reports/1077520)
披露的报道后，我说哇，2000 美元。对于父母的 pin 旁路，让我也绕过 pin，因为我有相同的@zseano 流，我黑了主应用程序

## 游戏开始了

我首先创建了一个帐户，然后点击每个按钮来了解应用程序如何工作
几分钟后，我发现了家长控制端点，现在我在孩子的个人资料中添加了一个密码，这样，没有 18 岁以上的孩子就不能观看成人系列

在浏览我的打嗝历史记录时，我发现了这个[https://www . showmax . com/eng/parental controlform/% 2f home/true](https://www.showmax.com/eng/parentalControlForm/%2Fhome/true)

我决定看看这个 url，因为大多数时候你可以通过从真到假，从假到真来绕过保护

当我复制并粘贴孩子个人资料中的 url 时，它泄露了家长 pin

# 复制步骤:

1.  登录您的 showmax 帐户
2.  添加家长 pin
3.  转到[https://www.showmax.com/eng/home](https://www.showmax.com/eng/home)
4.  点击手表 triller
5.  您将被要求提供家长密码
6.  在您的浏览器中输入此 URL[https://www . show max . com/eng/parental controlform/% 2f home/true](https://www.showmax.com/eng/parentalControlForm/%2Fhome/true)
7.  您将在不确认密码的情况下获得家长 pin
8.  现在，您可以更改家长 pin，并且可以绕过密码确认进行家长控制

这里披露的报道[https://hackerone.com/reports/1121169](https://hackerone.com/reports/1121169)

谢谢你花时间看我的报告

你可以在推特上找到我[https://twitter.com/moodiAbdoul](https://twitter.com/moodiAbdoul)