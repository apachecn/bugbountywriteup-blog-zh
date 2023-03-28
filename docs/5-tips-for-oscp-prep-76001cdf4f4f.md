# OSCP 预科的 5 个小贴士

> 原文：<https://infosecwriteups.com/5-tips-for-oscp-prep-76001cdf4f4f?source=collection_archive---------0----------------------->

![](img/668e5efaa8c99045df267fc48f84cf3a.png)

不祥的攻击性安全标志

# 介绍

许多 OSCP 的文章着重讨论在 PWK 的课程和实验中花费的时间。在注册之前，我花了大量的时间来准备这门课程，并且只花了 30 天的时间就通过了考试。请随意跳过以下部分，查看为本课程准备最充分的 5 个技巧！

# 我的 OSCP 经历

两年前，我第一次听说了 OSCP，知道这是我想实现的目标。这种形式有些独特和迷人:24 小时考试。完全实用——没有选择题。自动化工具的有限使用。这一切听起来极具挑战性。的确如此。

收到课程作业令人望而生畏，因为它们都是一次涌来的:实验室访问、380 页的教科书和数小时的视频。我能够在第一周内完成 PWK 课程以及大约 90%的练习。在课程期间，我恢复了网络中的盒子，并在它们的服务恢复后运行密集扫描。

完成课程材料后，我进入实验室，现在已经有了一份完整的被扫描主机列表。在接下来的几周里，我打开盒子，记录我的过程，改进我的工作流程，并诚实地习惯于与一些旧的系统和服务进行交互。在我进入实验室的最后一周，我检查了我的实验报告，以确保我没有遗漏任何东西。我的最终报告有 198 页——主要是因为包含了大量易读的截图和代码片段！

在我 30 天的实验室访问之后，我用我的考试尝试来评估我完成本课程的准备情况。我的策略是从缓冲区溢出机器开始，同时扫描其他 4 台主机，然后淘汰其中一个更容易的机器。这个计划加上我的实验报告将会给我超过一半的分数，还有足够的时间来获得通过考试所需的其他分数。

我使用了 BOF 盒子，一个更简单的盒子，并在考试后的 9 小时内让用户使用了两个中型盒子。我挣扎着，无法在我的任何一个中型箱子上遵循手动权限提升路线，这应该可以将我推至及格分数。

最初的考试尝试让我气馁，但总体来说，我对自己的表现感到满意，因为选择箱子很难，我选择安排第二次尝试，并希望不同的媒体主持人！在第二次尝试之前，我花了一些时间去寻找一些退役的 windows HackTheBox 机器，并保持对我的整个过程和工作流程的敏锐。

我的第二次考试进行得很顺利，我设法找到了两个中号箱子。有了这些机器后，我有足够多的分数通过考试，包括我的实验报告，我选择放弃硬机器，以便在我仍然可以访问 VPN 的时候完成我 39 页的考试报告。我在 20 小时内完成了考试并提交了报告。

[](https://www.youracclaim.com/badges/2977e03d-612c-4006-810f-454f271b512b) [## 攻击性安全认证专家(OSCP)是由攻击性安全颁发给米切尔…

### OSCP 能够研究网络、识别漏洞并成功执行攻击。这通常包括…

www.youracclaim.com](https://www.youracclaim.com/badges/2977e03d-612c-4006-810f-454f271b512b) 

# 1.HackTheBox & IppSec

[](https://www.hackthebox.eu) [## 黑客盒子::渗透测试实验室

### 一个在线平台，测试和提高您在渗透测试和网络安全方面的技能。立即加入并开始…

www.hackthebox.eu](https://www.hackthebox.eu) [](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) [## IppSec

### 你可能知道我的频道。这里有一堆我喜欢的其他内容。Patreon 页面上的酷人…

www.youtube.com](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) 

众多类似 OSCP 的 HTB 机器中的一个

HackTheBox 是一个非常棒的实践学习资源，如果没有它，我不认为我能够准备或构建一个适用于 PWK/OSCP 的工作流程。IppSec 关于退役盒子的视频非常棒，与 HackTheBox 提供的 DIY 学习方法配合得很好。我从他的每一个视频中学到了新的、无价的信息，以及解决我在盒子退役前遇到的一些问题的替代方法。

# 2.乔治亚·魏德曼

[](https://nostarch.com/pentesting) [## 渗透测试|无淀粉压机

### 渗透测试人员模拟网络攻击，以发现网络、操作系统和…

nostarch.com](https://nostarch.com/pentesting) [](https://www.cybrary.it/course/advanced-penetration-testing/) [## 来自 Cybrary 的免费高级渗透测试课程

### 我们免费的在线高级渗透测试课程为您提供技能，让您准备在一个…

www.cybrary.it](https://www.cybrary.it/course/advanced-penetration-testing/) 

乔治亚·魏德曼的材料是很棒的资源，你可以按照自己的节奏轻松进入 PWK 的内容。她提供了一门免费的电子图书馆课程，该课程与她的书密切相关。如果你想在复习 PWK 的教学大纲的基础上更进一步，但是还没有准备好注册这门课程，那么这份材料是非常值得推荐的。

# 3.dostackbufferoverflowgood

[](https://github.com/justinsteven/dostackbufferoverflowgood) [## justinsteven/dostackbufferovalgood

### 在 GitHub 上创建一个帐户，为 justinsteven/dostackbufferoverflowgood 开发做贡献。

github.com](https://github.com/justinsteven/dostackbufferoverflowgood) 

像许多人一样，我被从头开始编写缓冲区溢出的想法吓倒了，这是课程和考试都需要的。我寻找了许多资源，这一个最能帮助我思考这个话题。Justin 包括一个易受攻击的二进制文件以及解释他的过程和工作流程的阅读材料，这些内容完美地转化为实验室和考试的缓冲区溢出部分。

如果说他的方法中有一点值得我推荐的话，那就是他如何在免疫调试器 中使用 Mona 模块自动识别坏角色！这是迄今为止我所能找到的最复杂的方法，帮助我在 PWK 之前的练习中、课程练习中和考试中保持头脑清醒。

Georgia Weidman 对 WarFTP 和 Vulnserver 的报道也是很好的练习工具，可以让你更好地理解这个主题。

[](https://www.exploit-db.com/exploits/3570) [## WarFTP 1.65 —“用户”远程缓冲区溢出

### CVE-34041。Windows 平台的远程利用

www.exploit-db.com](https://www.exploit-db.com/exploits/3570) [](https://github.com/stephenbradshaw/vulnserver) [## stephenbradshaw/vulnserver

### 用于学习软件开发的易受攻击的服务器- stephenbradshaw/vulnserver

github.com](https://github.com/stephenbradshaw/vulnserver) 

# 4.不和谐社区

[](https://discord.gg/TN3jXaQ) [## 信息安全社区

### 使用现代语音和文字聊天应用程序加快游戏进度。清晰的声音，多服务器和渠道支持，移动…

不和谐. gg](https://discord.gg/TN3jXaQ) [](https://discord.gg/2G2nap8) [## OSCP

### 使用现代语音和文字聊天应用程序加快游戏进度。清晰的声音，多服务器和渠道支持，移动…

不和谐. gg](https://discord.gg/2G2nap8) [](https://discord.gg/n7ubFqq) [## PWK/OSCP 预科学校

### 使用现代语音和文字聊天应用程序加快游戏进度。清晰的声音，多服务器和渠道支持，移动…

不和谐. gg](https://discord.gg/n7ubFqq) [](https://themanyhats.club/invite/) [## 许多帽子俱乐部

### Many Hats Club 是一个专注于信息安全的团体，成员来自各行各业。我们提供信息安全…

themanyhats.club](https://themanyhats.club/invite/) 

这些小组非常适合讨论想法、寻找动力、避免精疲力尽、获得挑战方面的建议，以及回答有关课程材料的问题。这些不是剧透的地方，但通常是一个平台，可以说出一个想法或思路，并与同学们进行近乎实时的交流。我从这些社区中获益匪浅。

# 5.一拳两拳

我将介绍一个实用的小技巧，它是一个名为 onetwopunch 的便利工具，它以令人印象深刻的准确性帮助完成了大型实验室网络中的大量普通扫描。该工具使用 unicornscan 快速扫描主机上的每个 TCP 和 UDP 端口，然后将打开的端口传递给 nmap，您可以使用它指定标志。我用它在实验室和考试网络上轻松准确地运行我的扫描。

[](https://github.com/superkojiman/onetwopunch) [## 超级 kojiman/onetwopunch

### 使用 unicornscan 快速扫描所有打开的端口，然后将打开的端口传递给 nmap 进行详细扫描。…

github.com](https://github.com/superkojiman/onetwopunch)