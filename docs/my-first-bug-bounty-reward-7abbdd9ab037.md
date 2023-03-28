# 我的第一笔昆虫赏金

> 原文：<https://infosecwriteups.com/my-first-bug-bounty-reward-7abbdd9ab037?source=collection_archive---------1----------------------->

## 任何猎人最快乐的时刻。我所做的，从一些策略和资源开始

![](img/7a08f9fb1886436af1014e61a9ebcc5c.png)

# 注意

在开始讲述我的故事之前，我想澄清几件事:

*   我几乎不知道如何编码
*   没有计算机/网络安全背景
*   我的母语不是英语，对我来说英语是第二语言(我会说 3 种语言)
*   我数学很差

# 开始

那是 2018 年初。我记得我破产了。一点钱也没有，急需一些。我开始寻找新的收入方式，我知道网上是我唯一的选择。我只是不知道从哪里开始。在脸书，我看到了一个关于 2018 年十大猎人的帖子。打开列表，看到一大笔钱付给这些在网上做了“某些事情”的人。

# 好奇心

尽管我不知道那是什么，我还是开始在网上搜索“如何成为一个 Bug 猎人”。几个小时后，我发现外面有一个新的世界，大量的信息需要处理，以便我开始理解那是什么。此外，老实说，我浪费了很多时间看 YouTube 上的视频，那些人这样做只是为了获得像你我这样的人的观点，但最终，他们永远不会教你真正的东西。你需要非常聪明，明白好老师和表现得像老师的老师之间的区别。

# 情报收集

动手学习的最佳场所

*   [https://portswigger.net](https://portswigger.net)
*   Twitter(使用标签搜索)
*   脸书黑客组织(非页面)
*   YouTube(尽管对我来说没什么帮助)

# 选择你的弱点

我犯了我们在学习新事物时都会犯的同样的错误。我们想快速了解一切。这是一个很大的错误。一步一步来。尝试一次只熟悉一个或三个漏洞。不要一味的催学，这样做只会伤害你的成绩和抓到好报告的机会。我确实经历过很多次。一旦我开始学习 **XSS** 、**重定向**、**子域**、 **CSRF** 以及其他漏洞是如何真正工作的，两件美好的事情发生了。首先，我不再像以前那样需要 8 到 4 个小时才能找到漏洞，而且我知道如何找到一个好的漏洞进行报告。

# 神话

一进入今年的疯狂世界，你就会听到一些神话。

*   你会变得富有
*   开始永远不会太晚
*   你不需要知道如何编码
*   就找别人帮忙吧。

让我给你分析一下。你变得富有的唯一方法是如果你擅长这个，并且你的大部分发现是 p1/p2 报告。虽然我在 2018 年开始了，但大多数时候我认为太晚了，为什么？因为如果你在这里的时间足够长，你会注意到大多数曾经付费的报告，现在甚至不给你积分，并作为 N/A 关闭，更不用说重复的了。只有当你知道自己在做什么的时候，才不算太晚。当你有这方面的背景时。第一年就像一个盲人习惯他的新环境。也就是说，这只是最基本的。

## 几乎不知道如何编码

在深入 Bug Bounty 之前，我曾经为了好玩而用 Python 写一些基本的项目。尽管这是我的一个丈夫，但大多数时候我习惯于看某些代码，甚至不知道我在看什么，尤其是当涉及到 **Javascript** 的时候。试着去理解一下 **Javascript** 、 **PHP** 、 **CSS** 、 **HTML** 以及一切与后端相关的东西。这会让你在游戏中领先一步。

## 请求帮助..

我加入了每个论坛，脸书，不和谐，电报室/在线小组。每次我找到感兴趣的东西，我都试图在所有这些地方寻求帮助，却发现没有人愿意帮助你。事实上，他们只会嘲笑你问了“愚蠢”的问题，如果他们觉得你手头有一份很好的报告，甚至更糟，那只是浪费时间等待别人来帮助你，以 reddit 为例。唯一能帮助你的“人”就是谷歌。明智地使用它，在那里你会找到你问题的大部分答案。

# 好的..但是你什么时候告诉我们光荣的一天？！

让我们言归正传。首先，我实话实说。不是一次而是三次，都是在三天内的同一个星期，总共 2000 美元。这是在将近两年后发生的！做一名猎人并不容易，有太多的不眠之夜，以及许多你会认为这只是浪费时间的日子。你需要向前看，尝试更简单更好的东西。但是只要有决心，任何事情都可以做到。只要尽你所能去努力，你最终会得到它的。许多人甚至会在 1 个月甚至几周内第一次出现漏洞，但并不是每种情况都一样。这就是为什么你必须非常坚强，不要让任何事情阻止你成为你想成为的人。

## 这些是 vulns

*   子域接管
*   存储的*跨站点脚本*
*   DOM *跨站点脚本*