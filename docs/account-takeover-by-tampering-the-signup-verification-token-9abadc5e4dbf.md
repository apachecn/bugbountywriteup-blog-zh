# JWT 错误配置导致帐户被接管。

> 原文：<https://infosecwriteups.com/account-takeover-by-tampering-the-signup-verification-token-9abadc5e4dbf?source=collection_archive---------0----------------------->

大家好，

我是来自卡纳塔克邦贝尔高姆的法伊克·贾拉里。

只是想分享我发现的一个奇怪的 bug。

所以让我们开始吧。

![](img/365bc52c9380cb08f9a95f74eb6bbe69.png)

由于我不被允许透露公司名称，让我们把它当作“xyz.com”。

所以首先我注册了一个账户。之后，我得到了一个链接来验证我的电子邮件。

这种联系就像 https://app.xyz.com/authentication/verify-account?token = eyjhbgcioijiuzi 1 NII SINR 5 CCI 6 ikpxvcj 9……。

我验证了我的帐户，并开始测试 web 应用程序。

我喜欢玩密码重置功能，所以我开始测试密码重置端点。

我尝试了许多可能的方法来寻找密码重置链接中的错误，但我失败了。

然后我比较了验证帐户链接和密码重置链接。

他们都有 JWT 代币。

验证帐户链接:https://app.xyz.com/authentication/verify-account?token = eyjhbgcioijiuzi 1 NII SINR 5 CCI 6 ikpxvcj 9……。

密码重置链接:https://app.xyz.com/authentication/password-reset?token = eyjhbgcioijiuzi 1 NII SINR 5 CCI 6 ikpxvcj 9……。

这让我想到…

![](img/a77516ead5b1c6ffa5c7d7813a4c2944.png)

我通过使用[https://jwt.io/](https://jwt.io/)解码来检查这两个 JWT 令牌

这两个令牌具有相同的有效负载，如下所示

{
"_id": "123abc456 "、
"email": "abcd@gmail.com "、
"type": "resetPassword "、
"iat": 1632927658、
"exp": 1633014058
}

" type":"verifyaccount "用于验证帐户，并且

" type": "resetPassword "，用于重置密码。

这是两个令牌之间唯一的区别。

所以我拿了注册时收到的验证令牌。

将令牌中的“type”更改为“resetPassword ”,保持所有其他字段不变，并使用令牌重置密码。

我通过篡改令牌成功地重置了密码。

![](img/cdb0f4f1890370af529ea00b3debe35b.png)

这个错误的影响很小，因为我可以使用验证链接重置我自己帐户的密码。

我在想如何扩大影响…

然后我再次检查了令牌，发现令牌中也有“_id”参数。

所以我开始搜索 webapp 是否泄露了“user_id”

在 web 应用程序中，有一个创建团队并邀请其他用户加入团队的选项。

所以我去了 https://app.xyz.com/invite 并邀请了其他用户。

我在邀请用户的时候通过打嗝拦截了这个请求。

我很幸运，响应中有“user_id”。

所以很快我又篡改了令牌，编辑了“_id”参数。

最后，我可以用上述方法接管任何账户。

通过使用工具 [GAU](https://github.com/lc/gau) 我也能够找到一些泄露的账户验证链接。

通过命令“gau app.xyz.com”

这种病毒在几个小时内就被排除了。

也在一天内解决了。

谢谢你的阅读。

喜欢就分享吧。