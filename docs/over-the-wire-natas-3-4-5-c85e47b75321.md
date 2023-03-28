# 《过线》——纳塔斯 3 4 5

> 原文：<https://infosecwriteups.com/over-the-wire-natas-3-4-5-c85e47b75321?source=collection_archive---------3----------------------->

# 越线纳塔三级

![](img/30dbccd5f0e8cf4b090f53937e9bf38b.png)

Natas 三级

让我们去检查页面源代码

![](img/6dd03b52b65331975f331ea8bec3bb4d.png)

谷歌也不行

搜索引擎检查/robots.txt 文件中允许和不允许的页面

![](img/ff60b7098cd6d7f1f93b128db4005e16.png)

服务器上有一个/s3cr3t/目录

![](img/2fa58cf355b8d901e02ac22aebea19da.png)

包含文件名“users.txt”

该文件包含 natas4 的凭据 4

![](img/d8e64c6efe002fc307274d5812d01ff3.png)

这边请

# 四级以上

![](img/64157c22cc65f7d98aa577eef4a2b862.png)

Natas 级

该页面希望我们从 natas5 到达，因此更改 burp 上的 HTTP Referer 头

![](img/35d3d5a8edf6e3c995deba1cc5e68270.png)

原始请求/响应

![](img/8682d9e4ef25c72354263333ad70f24b.png)

请求在打嗝时修改

对修改后的请求的响应包含 natas5 的凭证

# 第五级

![](img/c90e28d4662a827b8c9819169b93a8e6.png)

Natas 级

检查源，那里什么也没有，所以检查打嗝时的请求/响应

![](img/9bc079f580be2c10df7dceeb7d6878b7.png)

服务器响应将“登录”cookie 设置为 0

![](img/d1d260925f23a3ac7b463726d8018545.png)

cookies 总是在给定时发送到服务器

让我们将请求发送到中继器，并更改 cookie 值

![](img/4256d52986c3481c0054215ff1c029be.png)

这边请

对修改后的请求的响应包含 natas6 的凭证

这是 OTW·纳塔斯的第 3、4、5 关

我希望你喜欢它。

PVXs—[https://twitter.com/pivixih](https://twitter.com/pivixih)