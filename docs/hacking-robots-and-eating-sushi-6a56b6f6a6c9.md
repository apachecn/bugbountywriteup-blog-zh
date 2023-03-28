# 黑客机器人和吃寿司

> 原文：<https://infosecwriteups.com/hacking-robots-and-eating-sushi-6a56b6f6a6c9?source=collection_archive---------2----------------------->

最近，我和一位老朋友共进晚餐， [Aprel](http://www.aprel.com) 的系统工程经理/高级软件开发人员 [Jesse Hones](https://www.linkedin.com/in/jesse-hones-7354b4166/) 。我记得当我们第一次见面时，他解释说他设计并编程了机器人以极其精确的水平测量无线电频率。快进十年；我是一个道德黑客，而他正在设计比以往任何时候都更复杂的机器人。所以我做了任何人都会做的事；我邀请他参加 [OWASP DevSlop 秀](https://aka.ms/devslopshow)并谈论黑客机器人。

![](img/a62169ebb9974608e9d9bf14db9a923b.png)

杰西·霍恩斯和他众多机器人中的一个。

他的回答是“还没有。我不能告诉你所有的漏洞是什么，因为我仍然需要那些来完成我的工作。”

兴趣被激起。

他解释说，以前机器人的固件都是定制的；每个系统都有自己独特的雪花。就像今天的定制软件一样，充满了漏洞，然而你一次只能破解一个系统。他解释说，最近这种情况已经改变，事情正在标准化，许多人使用相同的组件，这些组件都运行相同的固件。我问这是否像当年的 Windows XP，因为几乎每个人都在运行它，所以当发现一个错误时，每个系统都很脆弱。他答应了。

但是 Jesse 是一名开发人员，而不是安全人员，所以他用“我需要确保它正常运行”的镜头来看待它，而不是“我想让这个机器人执行我的命令！”一个道德黑客的观点。

![](img/d09ca8922396d62305211828a1110a82.png)

更多杰西的机器人。

很明显，我必须立即对情况进行威胁建模。可怜的杰西。

我:如果制造了恶意软件，让所有受影响的机器人停止生产，那该怎么办？

杰西:是的，这会很贵。

我:勒索软件呢？

杰西:<unhappy face=""></unhappy>

我:如果有人接管机器人，让它们在每 20 个芯片中植入某种东西来监视用户，会怎么样？作为供应链攻击？

杰西:是的，那会非常糟糕。然而，这种情况可能很快就会改变；我们可能会切换到 [Windows Embedded](https://docs.microsoft.com/windows/configuration/lockdown-features-windows-10?WT.mc_id=SheHacksPurple-Blog-tajanca) 。

我:好吧……但是如果一家公司使用机器人 stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu xnet stu，并慢慢地破坏他们的竞争对手会怎么样呢？所以他们永远无法完成对一个产品的研发？同时窃取他们的想法？想 [Stuxnet](https://en.wikipedia.org/wiki/Stuxnet) 遇上[迅达名单](https://en.wikipedia.org/wiki/Schindler%27s_List)。

杰西:…

我:万一有人用他们来挖矿比特币呢？机器人密码盗版！

杰西:我想-

我:如果机器人成为大部分网络的弱点，被黑客有规律地用作支点怎么办？

此时杰西已经求助于静静地等我冷静下来。我看着他。

我:机器人杀人了怎么办？

杰西:那就结束了。这个行业无法生存。这是不允许发生的。永远不会。

听起来我们需要更多的机器人黑客。

# **像这样的内容还有更多，查看我的书，** [**爱丽丝和鲍勃学习应用安全**](https://aliceandboblearn.com/) **和我的在线培训学院，** [**我们黑紫色**](https://academy.wehackpurple.com/) **！**

## 我有一个邮件列表，请订阅，这是免费的！