# 理解密码学:最好的方法

> 原文：<https://infosecwriteups.com/understand-cryptography-the-best-way-93d713246cdb?source=collection_archive---------2----------------------->

![](img/e6816bba50262cac897693f8b213cd46.png)

照片由来自[佩克斯](https://www.pexels.com/photo/woman-standing-near-old-stone-wall-with-carved-images-4577718/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[瑞秋·克莱尔](https://www.pexels.com/@rachel-claire?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

这是一个关于如何通过在线资源和一本书来理解密码学基础的指南。这条路线花费了我大约 20 天的时间，平均每天学习 5-6 个小时。

密码学的首次使用可以追溯到公元前 1900 年的古埃及，由于互联网和移动通信，它现在是世界上使用最广泛的概念之一。

当你读这篇文章的时候，你周围的空气中充满了信息，这些信息大部分都被加密了。事实上，你访问的最后一个网站使用了加密，你进行的最后一次 ATM 交易使用了加密，你发送的最后一封电子邮件使用了加密，你发送的最后一条 WhatsApp 消息使用了加密，甚至你打的最后一个电话在发送你的数字化语音之前都使用了加密。

作为一名技术爱好者，你一定对这些算法是如何工作的以及几个世纪以来密码学是如何发展的很好奇。是的，几个世纪，因为在埃及人之后，朱利叶斯·凯撒也使用加密技术与他的军队通信。他使用凯撒密码，也称为移位密码。然后，在第二次世界大战中，使用对称加密的恩尼格玛机发挥了重要作用。

沿着这条路线，您将学习对称和非对称密钥加密的基础知识，以及一些常用的协议，如数字签名、消息认证码(MAC)、SSL/TLS 证书和基于加密基础知识的哈希函数。

# **如何开始这次冒险？**

鲁尔大学的 Christof Paar 教授已经在他的 YouTube 频道上上传了 25 个关于密码学的视频讲座。每堂课大约 90 分钟，包含关于主题的详细信息。

这些讲座基于一本名为“理解密码学:学生和从业者的教科书”的书，该书长达 390 页，由 Christof Paar 教授和 January Pelzl 教授撰写。你可以在 crypto-textbook.com 的[网站上查看这本书的样章，也可以在](https://www.crypto-textbook.com/)的[亚马逊美国](https://www.amazon.com/gp/product/3642041000?ie=UTF8&tag=crypttextb-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=3642041000)或[亚马逊购买。](https://www.amazon.in/Understanding-Cryptography-Textbook-Students-Practitioners-ebook/dp/B014P9I39Q)

关于这个主题还有很多其他的书，我已经买了两本，但是如果你真的想以最好的方式理解这个主题，我强烈建议你买这本书。这本书有一个非常简单的语言，并包括有关事件的详细信息，导致所有这些密码算法。它有一些历史故事，在理解算法之前设置一些背景。

你会发现这些视频和这本书可以追溯到 2010 年，但是密码学在这十年里并没有太大的变化，几乎所有的课程仍然是相关的。他还在 2017 年更新了关于 SHA-3 哈希函数的章节。

# 好，怎么学习？

从第一堂课开始，完整地看一遍，如果有一个副标题不清楚，就倒回去再看一遍。90 分钟是一段很长的时间，所以如果你觉得舒服的话，以 1.75 倍或 2.x 倍的速度观看视频。

讲完后，打开书，看题目。讲课是以书为基础的，你会在第一次阅读本身就理解大部分概念。

现在，再次从头开始讲课，并随身携带这本书。开始看视频，跟着书上的题目走。完成一个副标题后，开始在书上做笔记。笔记对复习很有帮助，尤其是在你有考试或面试的时候。

这可能看起来是一个漫长而乏味的过程，但是如果你专心致志，你将会在一天内完成至少一个带笔记的讲座，并对主题有完整的理解。

加密算法很简单，只有数学部分需要更多的时间。密码学涉及很多数学，在开始这门课之前我很担心。我一遍又一遍地看视频，以熟悉数学概念。没我想的那么难。

在学习本课程的过程中，你会发现某些主题很难理解。这样的话，你应该看一些其他 YouTube 频道上的视频。你也可以阅读关于这个话题的文章，这样你就会熟悉这个话题。

其他一些推荐的加密 YouTube 频道有:

1.  [电脑爱好者](https://www.youtube.com/playlist?list=PLe2H4_ODIlLOuZUNCyGJ8ytZA59PezMmW)(强烈推荐)
2.  [刀子乐队教授](https://www.youtube.com/c/professormesser/videos)
3.  [t v nagaraju Technical](https://www.youtube.com/watch?v=YHICHYIaqF0&list=PLBhIctyfOJgCozo5MA5qNuyMkenRbIxlR)
4.  Sundeep Saradhi Kanthety
5.  [简单的片段](https://www.youtube.com/playlist?list=PLIY8eNdw5tW_7-QrsY_n9nC0Xfhs1tLEK)
6.  [未兑现门— ME，PI，XE](https://www.youtube.com/c/UnacademyGATE/videos)

如果你懂印地语，一些频道是:

1.  [阿布舍克·夏尔马](https://www.youtube.com/playlist?list=PL9FuOtXibFjV77w2eyil4Xzp8eooqsPp8)(强烈推荐)
2.  [5 分钟工程](https://www.youtube.com/playlist?list=PLYwpaL_SFmcArHtWmbs_vXX6soTK3WEJw)

其他一些基于密码学和网络安全的有趣视频/播放列表/频道:

1.  [保守秘密:互联世界中的密码学](https://www.youtube.com/watch?v=nVVF8dgKC38)
2.  [麻省理工学院 6.858 计算机系统安全，2014 年秋季](https://www.youtube.com/playlist?list=PLUl4u3cNGP62K2DjQLRxDNRi0z2IRWnNh)
3.  [沙德·斯鲁特的《信息安全》](https://www.youtube.com/playlist?list=PLhPyEFL5u-i10Bek21aMh4FdaOPR2SeEJ)
4.  [麻省理工学院 6.046 密码学，2014 年秋季](https://www.youtube.com/playlist?list=PLe2H4_ODIlLM1AQ5icfLm57qQeR59O9ug)
5.  [白帽卡尔聚](https://www.youtube.com/c/WhiteHatCalPoly/videos)
6.  麻省理工学院 15。S12 区块链与货币，2018 秋季

关于这个主题有很多资源和视频。开始上课时，我很困惑。但是现在，完成课程后，我觉得这是一条最简单的路。

关于加密的有趣事实:

1.  理论上，任何加密都可以被破解。实际上，要破解加密，我们需要强大的计算能力，我说的“强大”是指非常非常强大的计算能力。今天的加密即使使用世界上所有的超级计算机也无法破解。
2.  如果量子计算机在未来成为现实，那么通过使用强大的算法，它们可能能够破解今天的大多数加密。这意味着我们今天发送的所有加密信息都可能在未来被破解。只要消息被存储并在那时可被访问。这对所有政府的秘密信息都是不利的。
3.  第一个广泛使用的加密算法是由 IBM 开发并于 1975 年发布的“数据加密标准”。它后来在 1976 年被美国国家安全局修改。
4.  今天，高级加密标准(AES)是最广泛使用的加密消息的加密方法。AES 原名“Rijndael”，由两位比利时密码学家设计。
5.  当您打开网站时，挂锁(🔒)图标表示浏览器已经使用其 SSL/TLS 证书验证了网站的身份，并且您发送和接收的所有数据都将被加密。
6.  Whatsapp 使用 Signal 协议来提供端到端加密。该协议由信号技术基金会开发，该基金会还开发了信号消息应用程序。该协议使用非对称加密在用户之间交换密钥。然后，使用 AES 对称加密使用该密钥对消息进行加密。

# 感谢您的阅读。

在 iamnirajwagh.medium.com[](https://iamnirajwagh.medium.com/)**阅读我的其他文章**

# **反馈**

**我喜欢得到反馈，**积极的和消极的**。您可以通过以下方式与我联系:**

**[@推特](https://twitter.com/NiRajWAGHHH)**

**[@LinkedIn](https://www.linkedin.com/in/nirajwagh/)**

**[@GitHub](https://github.com/nirajwagh)**