# 让我们深入研究 OSINT:第 2 部分

> 原文：<https://infosecwriteups.com/lets-go-deep-into-osint-part-2-b9073be60a0d?source=collection_archive---------1----------------------->

这个博客我们将解决 OSINT 的一些挑战。

我们将使用 Tryhackme 的两个房间来解决与 OSINT 相关的一些挑战。

[https://tryhackme.com](https://tryhackme.com/)

让我们首先解决 OhSINT 房间

[](https://tryhackme.com/room/ohsint) [## TryHackMe | OhSINT

### TryHackMe 是一个学习和教授网络安全的在线平台，全部通过您的浏览器完成。

tryhackme.com](https://tryhackme.com/room/ohsint) ![](img/c308b30f694a3fb62454503f363d48ed.png)

让我们下载图片

![](img/18a898303e1f8ca67690f310c7c87ccd.png)

这是下载的图像

> **1 号**

![](img/068ccc6255940b0b323a6e2c0698937f.png)

让我们检查 exiftool 以查看图像的元数据

![](img/564d35bd5fa8fae1ee7f09878fdc4808.png)

看到‘版权’它的价值是‘OWoodflint’，这能把我们引向某个地方

让我们在 twitter 上搜索一下，因为 avatar 的意思是个人资料图片

![](img/4c813a7dee2e176640b189d6ba4598ef.png)

所以我们有了一个个人资料，头像是猫(个人资料照片)

所以答案是猫

> **2#**

![](img/c473f18aaea773b16682781ee7be5538.png)

现在我们需要找到这个“奥伍德弗林特”住在哪里。

现在让我们用谷歌搜索一下这个人

![](img/4655697699a2d75046be8fdff1e38450.png)

我们得到了很多关于这个人的信息。

现在在 github 链接中我们可以看到他住在伦敦

所以答案是“伦敦”

> 3#

![](img/2ac99850a67bc08aba5679f9d55731b8.png)

让我们深入互联网，以获得有关他所连接的网络的信息。

从他的推特账户上，我们看到了他的一条推文

![](img/b7b74d3bf993379a8f7f7cb44e56cf78.png)

我们得到了一个网络的 BSSID。让我们使用 wigle.net 获得这个网络位置

![](img/2ef980fe3008f370db990ed6336a297a.png)

通过使用它，我们得到 SSID 为“UnileverWiFi”

> **4#**

![](img/0867215d0f85336c9f79580c83893f0a.png)

我们已经对他进行了谷歌搜索，在访问了他的 github 个人资料上与他相关的链接后，他给了我们他的电子邮件地址

![](img/7ca9cad10a752004995eefa0b053868d.png)

> **5#**

![](img/b3a68c3973e11adfa89c3ca883d5cdc5.png)

我们在 github 上看到的

> **6#**

![](img/38317dfd7813ce2e1a0a496d725ff57a.png)

现在我们只需要谷歌搜索一下这个人

在谷歌上，我们可以看到他开了一个博客，所以在检查后，我们知道他在纽约，谈论一些照片，所以我们猜测这个人可能正在享受假期

![](img/ba1dd0497b35b92f3567a32066758817.png)

所以他去“纽约”度假。

> **7#**

![](img/7e043e70141d3075a30970c48ae52242.png)

这需要一些观察技巧，让我们先谷歌一下这个家伙

![](img/4655697699a2d75046be8fdff1e38450.png)

现在我们在“pennYDr0pper”下面的博客链接中看到。!'但是当我们访问博客时，我们没有看到这样的内容。

![](img/cb798ef82bc782b0e515525a457e31c8.png)

在查看源代码时，我们可以看到这个字符串是隐藏的

![](img/5553d91cab40b0b105f450ad04b6f0e6.png)

人们倾向于在源代码中隐藏他们的凭证，这样他们就可以记住它们。

所以我们得到了一个可能的密码

现在让我们解决另一个房间'地理定位图像'

[](https://tryhackme.com/room/geolocatingimages) [## TryHackMe |地理定位图像

### TryHackMe 是一个学习和教授网络安全的在线平台，全部通过您的浏览器完成。

tryhackme.com](https://tryhackme.com/room/geolocatingimages) 

> **任务 1**

![](img/75d60c70dbe3a151ecfe6a39daba1d38.png)

让我们下载挑战的图片

![](img/42d0d887592948fab9a0c86e5e5a1f29.png)

这些是下载的图像

> **任务二**

![](img/b91902a2edf790f770c5083a4a30fa6b.png)

好的，我们需要在 yandex 搜索中查看 1.jpeg 的图片

![](img/ff93f3d5525ef10d2c4dbc4db6bc7904.png)

Yandex 图片搜索提供一些相似的图片让我们看看这些图片

现在，在其他图像(类似的图像)中，我们看到一个包含这些数据的图像

![](img/e408006e8cb3d2c5fdd58df182834c3d.png)

所以这座纪念碑位于“中国”

> **任务四**

![](img/5aeb351f08c6fdae88854d1ff5ebf3f9.png)![](img/70956b1b56855d5dcf16c47b3d2144e9.png)

图片 2

好的，这张图片本身给出了很多细节。

第一个细节是这是一张来自网络摄像头的照片。

这张照片给出了像“谢夫利德”和“艾迪生”这样的地名

让我们在谷歌上查找这些地方

![](img/4df732d5272fe16e75634341c7358978.png)

这个地方在芝加哥，好的，我们现在有东西了，我们需要看看这个摄像头在哪里。

现在我们可以使用街景来适当地探索这个地方

![](img/f6ccdea7a4bd178c78ca3f5747d7fdf2.png)

答对了。！我们找到了准确的十字路口，现在只需要四处看看就能找到摄像头了

![](img/340830f95ba771687a6a671eada9238d.png)

现在，环顾四周，我们看到了“箭牌体育”商店的网络摄像头。

> **任务 6**

![](img/e7d2da7ca177c86769402966f5e2ad2f.png)![](img/35bcb32dcf6a87baae26a456d579918a.png)

图 3

现在在提示中我们可以看到

![](img/5e3a704176f45b7977f6b602a35e1e22.png)

这个地方是巴黎，现在我们需要准确定位这个位置

通过查看图像 3，我们确信它是从网络摄像头拍摄的。

所以让我们用谷歌搜索巴黎所有的网络摄像头

![](img/55a1935d162f51bc3f7f7c11b28bb8cd.png)

通过使用这个网站

[](https://www.webcamtaxi.com/en/paris-live-webcams.html) [## 巴黎直播网络摄像机列表

### 法国首都巴黎的网络直播。

www.webcamtaxi.com](https://www.webcamtaxi.com/en/paris-live-webcams.html) ![](img/7d34b69e27c55962d37484797256eb9f.png)

我们可以在巴黎安装网络摄像头。

让我们在里面搜索埃菲尔铁塔的网络摄像头

![](img/e3baead5c2feed8e0583290759806af9.png)

该集中注意力了第二张和图 3 一模一样

![](img/40af0b0eae3669b463ef679a5faf9029.png)

我们得到了摄像头的位置，让我们在谷歌上搜索这个位置

![](img/651e9b2f1cd657ac743673476edefaea.png)

所以它位于巴黎的默东

答案是“默东天文台”

不要以为每次一个工具或一个特定的网站都能给出答案，为了在搜索中取得成功，我们需要稍微修改一下，并在搜索中表现出色。

> **任务 7**

![](img/fed4a8294614ac582758bf1883166a18.png)![](img/f8df0946734bb19bb0de4fa82ca7f3d4.png)

图 4

如果我们观察这幅图像，它包含了一些信息

首先，它有一些东西写在墙上和贴纸上

因为汽车在移动，所以这是一个网络摄像头

这是一个十字路口，它显示了斑马线的照片

**灰色的车牌很明显，所以这是我们的线索**

![](img/50843471c50e18aa3c94d1eb573d6b19.png)

牌照上的号码是 LO10 PNE

让我们在谷歌上查一下这个号码，我们可以很容易地从中找到城市的位置

![](img/b622cab2c742218695cfd15e877e1031.png)

这是一辆伦敦的车，所以现在我们知道了这个十字路口所在的城市

既然它有斑马线，让我们在谷歌上搜索“伦敦的斑马线”，看看是否有什么有趣的东西。

![](img/9022656df3fe91bcb437d094f7e1c303.png)![](img/97cf0cdddca25ed3ca5d937798457ac5.png)

这张图片看起来很相似

![](img/02e83af7cbe4edb5476137f9f164e524.png)

我们可以看到道路的设计模式与图片 4 相似，因此我们可以确认这是完全相同的地方

通过查看道路和斑马线的设计，谷歌图片中的所有结果都是同一条道路。

![](img/46d7c01e652ad965f43d2051b95f61e6.png)

“世界上最著名的斑马线”哈！！！！！！

让我们访问链接找到位置

![](img/3e390fc0fb4df3e5db537c2539c8ed31.png)

让我们在这条修道院路上多找找。

![](img/af1cca6c7944dc9689aea300b6edf2c8.png)![](img/0dc1120ab43b64a3804450945ca6f28f.png)

看起来是这个地方，所以答案是“修道院路”

在这里，我们没有任何工具可以给出答案，但通过我们在谷歌搜索和近距离细节观察的技能，帮助我们获得了图像的位置。

作者- infosec_boy

[](https://www.linkedin.com/in/anubhav-mandarwal-318952186/) [## anubhav mandarwal - CTF 玩家和博客作者-网络叛逃者| LinkedIn

### 查看 anubhav mandarwal 在世界上最大的职业社区 LinkedIn 上的个人资料。anubhav 有 4 份工作列在…

www.linkedin.com](https://www.linkedin.com/in/anubhav-mandarwal-318952186/) [](https://twitter.com/infosec_boy) [## 阿努巴夫·曼德瓦尔

### anubhav mandarwal 的最新推文(@infosec_boy)。💻渗透测试、bug 搜索、信息…

twitter.com](https://twitter.com/infosec_boy)