# 工作场所标识 ID 到工作场所所有者姓名披露脸书 Bug 赏金

> 原文：<https://infosecwriteups.com/workplace-logo-id-to-workplace-owner-name-disclosurefacebook-bug-bounty-e745db59d0bd?source=collection_archive---------0----------------------->

嗨我是我 Ajay Gautam， [Nass](https://nassec.io) 的安全研究员，目前正在研究位(荣誉)计算。今天，我要分享我在 2018 年发现的一个脸书有效问题。

如果标识了工作区徽标的 ID，我可以通过他们的徽标 ID 看到工作区所有者的姓名。

当我们将活动的封面图片 id 替换为其他人的工作场所徽标 id 时，猜猜发生了什么？我很惊讶在回复中看到了主人的名字。

工作区所有者只能上传其工作区的徽标，工作区中公开的 ID 是管理员本身的 ID。

因此，在漏洞之旅中，首先，我在自己的工作场所创建了一个事件…

之后，我上传了一张活动的封面图片，并在新标签中打开了它。

封面图片上传成功后，我将 ***fbid*** 替换为另一个职场
的职场 logo id，显示的网址链接如下:
[https://workplace.facebook.com/photo.php?***fbid***= 111128102791845&set = GM . 107808558585673352&type = 3&theater](https://workplace.facebook.com/photo.php?fbid=111128102791845&set=gm.1078085585673352&type=3&theater)

最后主人的名字被公开了。

## 时间表

*   已报告—2018 年 5 月 31 日
*   审判—2018 年 6 月 16 日
*   奖励—2018 年 10 月 9 日

请查看 POC 视频，了解漏洞的详细说明。