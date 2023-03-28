# 意外的错误导致谷歌荣誉奖

> 原文：<https://infosecwriteups.com/accidental-bug-leads-to-google-honorable-mentions-7dad9eecbd7f?source=collection_archive---------4----------------------->

嘿，各位黑客和臭虫猎人们，

## 我的谷歌名人堂的故事

![](img/1310e78b3a6ce64d96d4783a49813f3a.png)

**Bug 名称:**错误信息泄露网站源代码。

**严重程度:**低

在 google bug hunters 网站中，google 提供了要搜索的目标。于是我选择了名为“*.onduo.com”的目标。乍一看，onduo 没有太多的功能可以测试。然后我做了 directory bruteforce，但也是以静脉结束。

![](img/5c7f932b6066bd061f41367299ef7e13.png)

我用 Subfinder 收集了 onduo.com 所有的子域。我不知道为什么我点击了名为“develop.onduo.com”的子域，这也和主网站一样。但是当我访问“www.onduo.com/blahblah”时，它会显示 404 页，我想"develop.onduo.com/blahblah"也是如此。但是当我访问 develop.onduo.com/blahblah 时，它披露了一些网站的源代码没有找到模板的错误信息。

我在 10 月 27 日早上 6 点向谷歌报告了这件事。我原以为谷歌会把这份报告当作“不可用/重复”来关闭。但是他们回答说**根据你的报告，我已经向负责的产品团队提交了一个 bug。**

![](img/3bd981bd87d6b25a4641d9cd3b052e27.png)

这是我在报告了 6 份报告后第一次被接受。

感谢您阅读这篇文章。

请关注我，获取更多关于寻找 bug 的文章

在 Instagram 上关注我:[https://www.instagram.com/ram_0x_infosec/](https://www.instagram.com/ram_0x_infosec/)

在 Linkedin 上联系我:[https://www.linkedin.com/in/ram0xinfosec/](https://www.linkedin.com/in/ram0xinfosec/)