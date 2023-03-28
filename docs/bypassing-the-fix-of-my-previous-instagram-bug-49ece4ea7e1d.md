# 绕过我之前 Instagram bug 的补丁。

> 原文：<https://infosecwriteups.com/bypassing-the-fix-of-my-previous-instagram-bug-49ece4ea7e1d?source=collection_archive---------0----------------------->

![](img/01d84cbeb9409d9426be338b559ab816.png)

**读者您好！**在这篇文章中，我将与你分享我是如何通过一个简单的逻辑漏洞打破 Instagram 故事限制而获得两次赏金的。

问题出在 Instagram stories 上，我能做的就是回复 Instagram stories，即使账户所有者已经设置了`Allow message replies to "off".`的隐私

# 下面是我的第一个 bug 的详细复制步骤:

1.  首先，我要做的是打开 Instagram story，它的回复是禁用的。
2.  现在，当我在故事中时，我会在另一部手机上给自己发一条 WhatsApp 消息，让我的键盘在故事中弹出。(该步骤也可以通过各种其他方式来完成)
3.  现在，当键盘在故事中弹出时，我注意到在特定的故事中有一个回复框。
4.  现在有了回复框，我可以轻松回复这个故事了。

> 脸书修复这个 bug 的方式是，他们不再允许在禁用回复的情况下，当键盘在故事中弹出时，回复按钮会出现，他们还奖励了我 3digit 奖金。

# 现在，我实际上是如何设法绕过这个修复的呢？

由于这只是一个基于 UI 的修复，我知道这仍然是脆弱的，我所要做的就是找到一种方法再次弹出键盘，让回复按钮再次出现。我尝试了各种方法，但似乎没有一种有效，直到我这样做:

1.  打开已启用回复的上一篇文章，以便自动显示的下一篇文章是已禁用回复的文章。
2.  现在，我会弹出前一个故事中的键盘，让键盘一直打开，直到故事结束，下一个禁用回复的故事出现。
3.  现在我的键盘已经打开了，故事的线索指向了禁用回复的那个，我的键盘仍然打开着，而且有一个回复按钮。
4.  现在有了回复选项，我可以再次回复这个故事了。

> 现在，这一次他们实现了服务器端修复，即使有人在禁用回复的情况下成功回复了 Instagram 故事，他/她也会收到消息未发送的错误。这次他们给了我 4 位数的奖金。

# 要吸取的教训:

1.  当您的 bug 得到修复时，尝试绕过修复并检查修复是否是一个完整的修复，有时安全人员可能会懒于从 bug 可能重现的每个方面实现完整的修复。

感谢您坚持到本文结束。

给我留个随从:[https://www.twitter.com/spongebhav](https://www.twitter.com/spongebhav)

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)