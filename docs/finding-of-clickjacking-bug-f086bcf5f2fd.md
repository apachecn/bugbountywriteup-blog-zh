# 我是如何发现点击劫持漏洞的

> 原文：<https://infosecwriteups.com/finding-of-clickjacking-bug-f086bcf5f2fd?source=collection_archive---------0----------------------->

## Bug 奖金记录

![](img/bfe8a75b151c4b3a847fcdd1361cdbc5.png)

欢迎回来神奇的另一个重要话题，我是如何发现点击劫持的错误。最初，在侦察阶段之后，我去了 SQL 注入、XSS、XXE 和 SSRF，但是我找不到任何有趣的东西。

之后，我寻找任何标题错过或没有，我开始知道 **X 帧选项**错过了。

那么就容易受到 ***点击劫持(UI 矫正)。***

> ***一种危险的技术，欺骗用户点击他们认为正在点击的东西以外的东西，这可能会泄露私人信息或允许他人在点击看似无害的对象(如网站)时控制他们的计算机。***

出于概念验证的目的，我使用了网站***click jacker . io***网站。本网站提供了 ***点击劫持(UI 矫正)的详细报告。***

粘贴任何网址，并检查它是否容易被点击劫持。

或者用手动的方法找到这个 bug。

> <title>点击顶升测试</title>
> 
> 本网站易受点击劫持攻击！
> 
>  [”<a](”<a)
> 
> https://www.redacted.com/" width = " 1000 " height = " 500 "></iframe>

**缓解点击劫持攻击:**

为了避免点击劫持攻击，您必须启用 X-Frame-Options 和 CSP-header。

这是我的新博客页面，其中包含网络安全的提示

[](https://yo.fan/ethicalhacktech8) [## @ ethicalhacktech8 | YoFan

### 学习保护和如何保护免受攻击。

哟.范](https://yo.fan/ethicalhacktech8) 

通过查看内容来支持我