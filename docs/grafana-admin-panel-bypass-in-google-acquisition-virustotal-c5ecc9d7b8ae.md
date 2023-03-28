# 谷歌收购中的 Grafana 管理面板旁路(VirusTotal)

> 原文：<https://infosecwriteups.com/grafana-admin-panel-bypass-in-google-acquisition-virustotal-c5ecc9d7b8ae?source=collection_archive---------1----------------------->

我从谷歌收购案(VirusTotal)的常见子域侦查开始。这一次我使用了在线子域搜索服务[https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/)来快速找到子域。

然后我发现了一个子域[grafana.internal.virustotal.com](https://grafana.internal.virustotal.com/)，子域中的内部一词让我出于好奇访问了那个页面。

但遗憾的是，它只针对授权用户。我搜索 grafana 端点访问/注册，并试图注册新帐户，它显示注册被禁用。

然后我想如果 oath sign in 被允许的话，我可以试试用 Google，Github 或者其他任何服务登录。但是新用户注册时一切都被禁用了。

我知道登录的唯一方法是使用现有的用户名和密码。因为是谷歌收购，我不想尝试对用户名和密码的暴力攻击(管理员可能有非常强的用户名和密码)。

好吧，一切都是徒劳的，我想探索更多的终点，所以访问了 grafana 的文件。我遇到了[https://grafana.com/docs/grafana/latest/http_api/user/](https://grafana.com/docs/grafana/latest/http_api/user/)，其中提到了添加、编辑用户和更改密码的端点。但是提到的功能中很少只有管理员可以使用。

我还注意到，在 docs 页面中，`Authorization: Basic YWRtaW46YWRtaW4=`在大多数敏感请求中都是作为基本的 Auth 头发送的。上面的标题是 base64 编码的，所以尝试解码，结果令人惊讶:

```
admin:admin
```

因此，基本的验证用户名和密码只是 admin:admin。

所以我做了一个快速的概念验证，我在 Firefox([https://mybrowseraddon.com/modify-header-value.html](https://mybrowseraddon.com/modify-header-value.html))中安装了修改头值扩展，并将其配置为在对[grafana.internal.virustotal.com](https://grafana.internal.virustotal.com/)的所有请求中发送基本授权头。

令人惊讶的是，我是以管理员身份登录的，拥有完全的权限。我还为自己创建了一个新用户并授予了管理员权限，创建了一个管理员 api 密钥，还使用生成的 api 密钥发送了一个带有简单 curl 请求的 poc，以证明我对应用程序拥有完全访问权限。

```
curl -H “Authorization: Bearer API_KEY” [https://grafana.internal.virustotal.com/api/dashboards/home](https://www.google.com/url?q=https://grafana.internal.virustotal.com/api/dashboards/home&sa=D&usg=AFQjCNF9CSi4maQcZc3ldDT9_RNzdjET_w)
```

![](img/ead3fcde5ee8f69ff511af1f1129b3e3.png)

Grafana 管理面板(以管理员身份登录)

此外，我还遇到了一个页面，这是通过管理面板泄漏电报 api 密钥。电报 api 密钥被用作向 virustotal 的内部电报组发送通知的挂钩。

我再次使用一个简单的 curl 请求生成了一个快速 poc，通过它我可以使用 bot 向任何人发送电报消息。

```
[https://api.telegram.org/bot_API_KEY/sendMessage?chat_id=userid&text=](https://www.google.com/url?q=https://api.telegram.org/bot443945044:AAF9DmJHMwwP2FheUkD04BlUf4pt3i5YA0Y/sendMessage?chat_id%3D-1001130484968%26text%3DHi%2520%250AThis%2520is%2520Jayateertha%2520G%252C%250A%250AI%2520reported%2520a%2520grafana%2520admin%2520panel%2520bypass%2520issue%2520earlier.%250Ahttps%253A%252F%252Fissuetracker.google.com%252Fissues%252F176016146%250A%250AThanks%2520and%2520Regards%2520%250AJayateertha%2520G&sa=D&usg=AFQjCNHuDWWtUhBBUelNcXBwHjKcNDDazg)SOME_URL_ENCODED_TEXT
```

因此，我对 grafana 面板有管理员权限和控制权，对 telegram bot 也有控制权。

我做了一个详细的 poc，提到了重现 bug 的所有步骤以及影响，并提交给了 GoogleVRP。这个 bug 被接受并奖励了 xxx 美元，因为它是谷歌的收购。

喜欢我的文章吗？在 Twitter([**】@ jayaterthag**](https://twitter.com/jayateerthaG)**)和 medium 上关注我，了解更多关于 bugbounty、Infosec、网络安全和黑客攻击的内容。**