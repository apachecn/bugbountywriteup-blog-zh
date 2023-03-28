# 哪些公司应该考虑设计一个 Bug 赏金计划

> 原文：<https://infosecwriteups.com/what-companies-should-consider-designing-a-bug-bounty-program-6e2e5e2ac773?source=collection_archive---------0----------------------->

![](img/6935a32e91fa2d470f85e25505c82a0d.png)

戴维·兰格尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

几周前，我在 PayPal 的一个 API 中发现了一个漏洞，该漏洞很容易被滥用，导致超额欺诈性收费，买家在不知情的情况下被收取了超过他们同意的费用([点击此处了解更多信息](https://faui1-files.cs.fau.de/public/publications/df/df-whitepaper-19.pdf))。当然，我向 PayPal 的 bug bounty 计划提交了报告，然而，该报告被拒绝，因为欺诈、盗窃或恶意商家帐户不在范围内。

我向其解释影响的所有人也对报告被否决感到惊讶，因此这让我思考:公司应该考虑什么来设计一个好的 bug 赏金计划？在这篇文章中，我分享了我对这个话题的想法，当然，如果你有不同的看法，请随时与我讨论。我对其他职位感兴趣。

# 为什么你的公司应该有一个 bug 赏金计划？

在我开始之前，首先让我回答一个问题，为什么一个公司应该有一个 bug 奖励计划。你应该永远记住:你的产品永远不会没有 bug！防止 bug 几乎是不可能的，尤其是如果你经常给你的产品添加新功能的话。即使你雇佣了最好的安全专家来检查你的程序，他们也很可能会监督一些事情。有了 bug bounty 程序，你就给了每个人分析你的程序并找到可利用的东西的机会。因此，你利用了群体知识——这通常是一件好事。每个人都有他或她自己的经验和专长，因此有新的和独特的想法来测试你的程序并找到漏洞。

# 让你的范围清晰易懂

首先，让我们考虑一下我在 PayPal bug bounty 项目上的经历。就像 PayPal 一样，我对他们的范围感到困惑——欺诈性的商家账户不在范围之内。虽然我知道恶意商家不是系统的缺陷，但我认为导致欺诈的缺陷应该在程序的范围之内。我不是唯一一个不同意 PayPal 业务范围的人，还有其他各种研究人员。在提到的例子中，研究人员发现了如何绕过双因素认证(2FA)的漏洞，然而，黑客必须拥有受害者的凭据才能绕过 2FA，根据 PayPal 的规则，这超出了范围。我同意研究人员的观点，并且会说，绕过 2FA 绝对应该在一个计划的范围之内。

因此，我认为公司定义一个清晰易懂的范围是至关重要的。否则，它会让那些对项目范围感到困惑的研究人员望而却步。而且，你会收到超出你范围的报告，结果会让研究人员生气。他们中的一些人可能再也不会看你的 bug 赏金计划了，比如前面提到的研究人员。

# 范围很广

当然，你的范围应该是可以理解的和清晰的，但几乎同样重要的是一个广泛的范围。也就是说，不要把你的 bug bounty 程序仅仅局限在你的主网站上，还要包括所有的子域名(*.example.com)、暂存服务器、VPN 服务器……只要你说得出的。当然，有些系统太重要了，不会被粗心的猎人损坏，但是，对于超出范围的物品，要三思而行。攻击者也不会停留在你的范围内。

例如，如果你用*包括所有子域，研究人员将搜索子域接管。因此，攻击者找到了一个很长时间没有使用的旧子域，它指向一个攻击者可以控制的地址，比如说一个 AWS bucket。然后，攻击者可以注册 AWS 桶，并以您的名义发送电子邮件或创建一个钓鱼网站。这就是为什么:让你的视野变宽！

# 提供测试环境

提供一个研究人员可以测试你的产品而不用担心造成损害的平台是非常好的。PayPal 就是一个很好的例子，你可以创建商家和顾客来处理假币。这让研究人员有可能测试他们原本会用自己的钱去做的一切事情。

此外，如果你的程序提供付费服务或高级账户，给研究人员一个高级账户来测试高级功能可能是个好主意。与测试环境一样，他们可以检查他们会跳过的功能，因为这会花费他们金钱。如果你不想提供一个完全免费的高级账户，就给他们一个有一些限制的高级账户——要有创意！我觉得值得。

# 提供下载的程序或应用程序

这听起来可能很简单，但我更喜欢提供安全链接来下载应用程序的程序。相应地，你的开发团队和 bug 赏金猎人在同一个版本上工作，这样可以避免误解。此外，猎人不需要从 APK 镜报或类似网站下载，这些网站看起来总是有点可疑。我想你明白我的意思。

# 提供有关基础设施的信息

请给出一些关于您的基础架构的见解。告诉研究人员每个域的用途。你用什么软件？这可以节省研究人员的时间，让他们更好地了解你的公司。攻击者无论如何都会发现的。

只要在你的 bug 赏金程序中提到“我们使用 XY 作为我们的后端解决方案。我们的 API 基于 GraphQL…”。这不是强制性的，但我相信这是一种姿态，表明你对 bug bounty 社区的信任。

# 钱钱钱

最后，当然，你应该考虑给分析师一个适当的报酬。金钱当然总是有激励作用的，但即使你只是一个预算很少的小公司，也要分发一些 t 恤、高级会员，或者为最活跃的黑客举办一个小活动。向他们展示你欣赏他们的工作，他们会继续黑你的程序。

# 结论

一般来说，不要让你的程序过于复杂，给 bug 赏金猎人最大可能的自由。如果他们有问题或意见，要帮忙并负责任，记住是你想从他们那里得到什么。此外，请记住，有一个 bug 赏金程序，即使有一大笔赏金，也不能保证你的程序没有 bug。只能说明还没人发现 bug！

正如我在开始时提到的，这些只是我的想法，我会对其他意见感兴趣。例如，你负责公司的 bug bounty 项目，并且不同意上述观点，我想知道为什么——我总是愿意重新考虑我的意见。

[](https://twitter.com/fh4ntke) [## 弗洛里安

### 弗洛里安的最新推文(@fh4ntke)。计算机系学生；对 IT 安全和取证感兴趣

twitter.com](https://twitter.com/fh4ntke)