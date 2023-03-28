# 重置密码令牌导致帐户被接管

> 原文：<https://infosecwriteups.com/reset-password-token-lead-to-account-takeover-19e5eb3cf816?source=collection_archive---------1----------------------->

你好，网络安全极客们，

这是我第一篇关于我在私人责任披露项目中发现的漏洞的文章。

我们开始吧，

让我们把目标定为**redacted.com**。我在忘记密码页面上发现了这个漏洞，漏洞存在于他们的一个子域上，比如 xyz.redacted.com 的**，页面看起来像下面的截图:**

**![](img/67b6550b762e79deaa2b81ce4095d1d0.png)**

**当我使用电子邮件捕获请求时，说【test@testing.com 请求看起来像**

```
POST /password/email HTTP/1.1
Host: xyz.redacted.com
Cookie: XSRF-TOKEN=value1; xyz_redacted_session=value2
Content-Length: 72
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="92", " Not A;Brand";v="99", "Google Chrome";v="92"
Sec-Ch-Ua-Mobile: ?0
Upgrade-Insecure-Requests: 1
Origin: [https://](https://planner.305.nl)xyz.redacted.com
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.131 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: [https://xyz.redacted.com/password/reset](https://planner.305.nl/password/reset)
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close_token=**CIgGjmQP48yc37hZMlQGwTsLeR6OlC1HABRgV8Vq**&email=test%40testing.com
```

**您可以看到密码重置令牌是通过 **_token** 参数泄露的，当我在 URL 中使用该令牌时，密码重置令牌链接正在工作，请参见下面的截图。**

**![](img/21b560484d8b4ce0d0490531d56dce4f.png)**

**现在，我只需知道他们的电子邮件，就可以接管任何用户帐户😎。**

**点击此处查看视频 POC:[**点击此处**](https://youtu.be/fKk3che2pkg)**

**订阅我的 youtube 频道寻找 bug 相关的东西: [**redirect _poc**](https://www.youtube.com/channel/UCq7-Qf45etdk0qc35I_n7PQ?sub_confirmation=1)**

**如果你喜欢 POC 和视频，你可以在 Instagram 上关注我**

**在 Linkedin 上关注我: [**我的 _linkedin**](http://linkedin.com/in/anurag-verma-650b771a2)**

**给我买杯咖啡😍:[此处](https://www.buymeacoffee.com/redirectpoc)**

**Infosec Writeups 团队刚刚完成了我们的第一次虚拟网络安全会议和网络活动。我们有 16 位出色的演讲者，他们主持了非常有价值和鼓舞人心的会议。要查看演讲者和主题列表，请点击此处。**

**[](https://iwcon.live/) [## IWCon2022 — Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)**