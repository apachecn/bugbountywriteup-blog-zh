# 通过用户代理的 HTML 注入导致网站失真，暴露后端代码。

> 原文：<https://infosecwriteups.com/html-injection-via-user-agent-leads-to-website-distortion-revealing-backend-code-304fee8f211c?source=collection_archive---------3----------------------->

亲爱的读者们，你们好，

这是我的第三篇关于 HTML 注入发生在用户代理头，导致网站页面失真和暴露后端代码的文章。

我们开始吧

在阅读以下解释之前，请点击此处查看完整视频 POC:[**点击此处**](https://youtu.be/wwV_20MrWTI)

主目标页面上存在的错误显示为【abc123.com ，看起来正常，但当我捕获请求时，请求看起来正常，我尝试了各种方法，但都无效，但当我分析响应时，我看到**用户代理**文本/值在反射，所以我制作了一个带有 HTML 标签的有效负载，但我也尝试了 XSS，但这些标签被阻止，但一些 HTML 标签可以正常工作，当执行时，它用代码扭曲了后端，您可以在我录制的视频概念验证中找到更好的解释。

在我的 youtube 频道查看更多概念验证:[**YouTube _ channel**](https://www.youtube.com/channel/UCq7-Qf45etdk0qc35I_n7PQ?sub_confirmation=1)

给我买杯咖啡:[https://www.buymeacoffee.com/redirectpoc](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbGdiN0NKSmhNWHAtODFBUnZWY05nVU9yZURYQXxBQ3Jtc0tubkdlQmxzQ1lMZDdJSThxS285c1NJY1hHSXdlbGRfOXlZcThYWE1lNng1d0VIemtlUi1VeEtWcHhnMjBQNXFFUmFicndXNkcyQWVjbk1Tbi01cmlJRFRQRG5qamNYZVFTaG42ekVETHpTUnNxUjB1Yw&q=https%3A%2F%2Fwww.buymeacoffee.com%2Fredirectpoc)