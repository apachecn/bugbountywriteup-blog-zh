# 解雇 8 帐户接管方法

> 原文：<https://infosecwriteups.com/firing-8-account-takeover-methods-77e892099050?source=collection_archive---------0----------------------->

![](img/d1d8896f238aaee3c72f398a705c9faa.png)

[目标](https://unsplash.com/@arget?utm_source=medium&utm_medium=referral)在[未飞溅](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄的照片

你好！这是来自孟加拉国的臭虫赏金猎人马鲁夫·霍桑博士。
我将推出一些账户接管方法

1.  **Unicode 规范化问题** 1。受害者账号`victim@gmail.com`
    2。使用 Unicode 创建帐户
    示例:vićtim@gmail.com
    Unicode 字符列表:[https://en.wikipedia.org/wiki/List_of_Unicode_characters](https://en.wikipedia.org/wiki/List_of_Unicode_characters)注:检查不需要验证的地方
2.  **授权发布**1。更改账号`A`的邮箱，放入邮箱`B`
    2。查看账户`B`中的确认邮件
    3。从账户`C`打开确认邮件
    接管账户`C`
3.  **重用重置令牌** 如果目标允许您重用重置链接，则通过`gau`、`wayback`或 url `scan.io`寻找更多重置链接
4.  **预占接管**
    1。注册使用正常的注册形式作为黑客，但黑客没有验证链接。
    2。如果受害者使用 oauth 注册。
    3。验证绕过现在攻击者可以登录受害者帐户没有验证链接与密码，他在注册时输入。
5.  **CORS 错误配置到账户接管**
    1。检查 api，任何端点都可以访问`access token/session/secret/fingerprint`
    2。如果是，请检查 CORS 配置是否错误，它是否允许我们从目标获取数据？
    3。制造一个有效载荷以获取数据并替换割台和吊杆
6.  **Csrf 到账户接管** 如果基于 cookie 的认证中的配置文件修改没有生成任何令牌
    1。开立帐户`A`更改&发送您自己的电子邮件，点击保存拦截请求并生成 csrf poc。
    2。如果完全基于 cookie 的认证，那么你不必修改任何东西发送 csrf 文件给受害者。
    3。如果它需要 GUID 用户标识或唯一令牌，这就变得很难做到，但这并不意味着它是安全的，只是开始玩目标
    提示:密码重置页面有助于多次 UUID/GUID 和用户标识
7.  **主机头注入**井
    井在这种情况下有 4 种方法可以做到。
    1。点击重设密码更改`host`标题。
    2。或者改变代理头 ex: `X-Forwarded-For: attacker.com`
    3。或者立刻将`host`、`referrer`、`origin`表头改为`attacker.com`
    4。单击重置，然后单击重新发送邮件，并做以上所有 3 种方法
8.  **响应操纵** 1。代码操作*到`200 OK`
    2。代码和主体操作
    code * to `200 OK`
    body * to `{"success":true}`或`{}`
    它在 json 被用来传输和接收数据时工作。

在推特上踢我: [@0xmaruf](https://twitter.com/0xmaruf)

在 twitter 上分享:

[https://twitter.com/0xMaruf/status/1582586936136384512?t = _ owdtwpqf 1 speurmixwbpa&s = 19](https://twitter.com/0xMaruf/status/1582586936136384512?t=_oWdTwpqf1SpEurmIXWbpA&s=19)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！