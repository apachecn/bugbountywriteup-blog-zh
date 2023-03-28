# TryHackMe-生物危害 CTF 报道(超详细)

> 原文：<https://infosecwriteups.com/tryhackme-biohazard-ctf-writeup-detailed-3d9dd491539e?source=collection_archive---------1----------------------->

![](img/b84a558dd224b4d045c4a9897689a10f.png)

CTF 报道#19

欢迎各位！！我们将在 [TryHackMe](https://medium.com/u/dc49a0a3cb16?source=post_page-----3d9dd491539e--------------------------------) 做生化危机 CTF。这是一个基于谜题的 CTF，灵感来自标志性的生化危机系列。如果你以前玩过 re 游戏，那么你会知道 RE 游戏是疯狂的，有很多部分，要找到钥匙，要制造或打破雕像，这是一个非常可怕的冒险。

我做这个盒子很开心。这是史诗 CTF！！在我们开始之前，我想告诉你这篇文章会很长，但绝对值得努力。😃

[](https://tryhackme.com/room/biohazard) [## 生物危害

### 一个基于老式生存恐怖游戏《生化危机》的 CTF 房间。你能坚持到最后吗？

tryhackme.com](https://tryhackme.com/room/biohazard) 

向 CTF 创造者大声喊出来[超棒的房间，为你的创造向你致敬！！]:

[](https://tryhackme.com/p/DesKel) [## TryHackMe |桌面

### TryHackMe 是一个学习和教授网络安全的在线平台，全部通过您的浏览器完成。

tryhackme.com](https://tryhackme.com/p/DesKel) 

在桌面上为您的 CTF 计算机创建一个目录，并在 CTF 目录中为 Nmap 创建一个目录。

![](img/709584f15c0149a045a01ffebf253e16.png)

让我们潜水探险吧！！享受噩梦吧！！

## 任务 1-简介:

![](img/57e8cea591d56121ecffef9a6e1d0a4f.png)

任务 1 列表

最简单的任务。😄

> #1.部署机器，开始噩梦。
> 答:不需要回答

## Nmap 扫描:

> *nmap-sC-sV-oN nmap/生物危害<TARGET _ IP>*
> 
> -sC:默认脚本
> -sV:版本检测
> -oN:输出将存储在您之前创建的目录“nmap”中

![](img/1c27eb9016c59803cb139976382033d7.png)

有 3 个端口打开:
21/FTP-vsftpd 3 . 0 . 3
22/ssh-OpenSSH 7.6 P1
80/http-Apache httpd 2 . 4 . 29

> #2.有多少个开放的港口？
> 答案:3

让我们检查一下服务器上的页面。

> 导航到 http://<target_ip></target_ip>

![](img/25a1770df2c354f27e0f80c4d1e8031c.png)

太好了。所以我们已经获得了球队的名字和其他角色的名字。有一个僵尸攻击和团队跑到附近的大厦。

> #3.行动
> 中的团队名称是什么

到目前为止一切顺利。检查页面的源代码是否有隐藏的注释总是好的。查看 URL 页的源代码。在这个房间里，我们将非常频繁地检查源代码。

![](img/6ec7fcd5a7e5efef53cb7809aa6493d1.png)

我们得到了去大厦的指南。点击它。

## 任务 2-大厦:

![](img/df0746a366420122ccdc39d49905c33d.png)

任务 2 列表

> 已重定向至 http:// <target_ip>/mansionmain</target_ip>

![](img/a684f22e79fc25016eddb53e2f5a352b.png)

是的，你想得对。检查源代码。[Ctrl+U]

![](img/9bc2be99112d21d8a30d896deae6b98a.png)

有一个评论要求我们去餐厅调查枪击事件。

> 导航到 http:// <target_ip>/diningRoom</target_ip>

![](img/b9513550c84e33b994f8e702edd7ffda.png)

单击“是”接受它，您将看到会徽旗帜。

![](img/b4f477fb6562e3b00f9f5066ca1ef8ac.png)

提交吧。

> #1.什么是会徽旗
> Ans:会徽{ XXXXXXXXXXXXXXXXXXXX }

刷新页面，我们将看到输入字段，我们必须输入会徽标志。

![](img/d6fe93eeba5089a8f4dd7fe1074366de.png)

输入并提交。

![](img/99d9bf11205d190d0628a6df6638a28c.png)

什么都没发生。需要做点别的。

回想一下，我们总是要检查源代码。[Ctrl+U]

![](img/71cff721c57d46b7c4debf4bc871899d.png)

有一个 base64 哈希，我们将解码它。我们可以在终端里做。

![](img/d32dec58020df7e357d45f7aa0b82b6c.png)

太好了！！我们得到了一本目录。我们将导航到它。

> 导航到 http://<target_ip></target_ip>

![](img/c98bcfab7df39ab00089afac0f24a8ec.png)

我们有一个目录/艺术室和一个包含开锁标志的开锁链接。

![](img/b566cdc60b9d9d2b285d91403fc5308c.png)

做得好！！提交吧。

> #2.什么是锁选标志
> Ans:lock _ pick { xxxxxxxxxxxxxxxxxxxxxxx }

在转到 artRoom 目录之前检查源代码。

![](img/857092cc8b9b31fd2f94c51947ed1ddb.png)

看起来很好。没有隐藏的评论或散列。继续前进…

> 导航到 http:// <target_ip>/artRoom</target_ip>

![](img/c970fd8a024622b692bb68cf92aa586b.png)

显然我们会调查的。单击是。我们将被重定向到大厦地图。该页面将包含所有我们已经去过的目录和我们将要去的目录。

![](img/69d8865b8d689ec326306052b40d4e73.png)

我们已经去过餐厅、茶室、美术室，因此我们将继续列举酒吧。

检查源代码。[Ctrl+U]

![](img/c0de96afad5d858cbc7b7c95df611c27.png)

看起来很好。我们继续吧。

> 导航到 http://<target_ip>/酒吧</target_ip>

![](img/20578718711f8e7d5a5045304b1261dd.png)

检查源代码[Ctrl+U]

![](img/e63a6c80e49ae3be447f682edb33cde3.png)

看起来又好了。

我们必须输入开锁标志，然后单击提交。我们将被重定向到一个页面。

> 已重定向至 http://<target_ip>/barroom xxxxxxxxxxxxxxxxxxxxx</target_ip>

![](img/2f95b8d711d097e55b4ffa01ab2a6350.png)

我们需要输入钢琴旗。检查源代码。[Ctrl+U]

![](img/6def8f2ad49f5d84631018e8492ac3c1.png)

我们会读音符。希望它包含音乐表标志。

![](img/86265b5fe500d2824b2b2cc5e8d47ce1.png)

有一个很长的散列，我们需要解码它。但首先，我们必须识别或分析它。

[](https://www.tunnelsup.com/hash-analyzer/) [## 哈希分析器

### 5 F4 DC C3 b5 aa 765d 61d 8327 deb 882 cf 99 MD5 5 baa 61 e 4c 9 b 93 F3 f 0682250 B6 cf 8331 b7ee 68 FD 8 SHA1…

www.tunnelsup.com](https://www.tunnelsup.com/hash-analyzer/) ![](img/fc69468607d0390e8f341d12d2442ce7.png)

哈希是 base 编码的，但我知道它不是 base64。没关系，我们会找到答案的。我破解哈希的在线工具是:

[](https://gchq.github.io/CyberChef/) [## 网络咖啡馆

### 网络瑞士军刀-一个用于加密、编码、压缩和数据分析的网络应用程序

gchq.github.io](https://gchq.github.io/CyberChef/) 

我们可以检查所有的基本格式，并很容易找到正确的格式。哈希是 base32 编码的。

![](img/94b1a7e2410a0deee462752bf9a7b8ac.png)

太棒了。！我们拿到了乐谱旗。提交它，我们就可以走了。

> #3.什么是乐谱旗
> Ans:乐谱{ XXXXXXXXXXXXXXXXXXXXX }

我们需要在页面中输入音乐表标志，然后单击 Submit。

![](img/2aadffb2cffee31d558a169cca70a7c0.png)

> 重定向至 http://<target_ip>/barroom XXXXXX/barroom XXXXXX . PHP</target_ip>

![](img/c26f266a46649cfcad150bd55ada10be.png)

显然我们会接受的😃。单击是。

![](img/bde5010fcc99a6a7bd59385f021ad32f.png)

太棒了。得到了金徽旗。提交吧。

> #4.什么是金徽旗
> Ans:gold _ 徽{ xxxxxxxxxxxxxxxxxxxxxxxxxxxxx }

检查源代码。[Ctrl+U]

![](img/c56ad21a7ad2b3e0726bbfdd12d35d40.png)

看起来很好。
输入会徽标志，点击提交。注:会徽旗不是金 _ 会徽旗

![](img/80a5a9744cb379f8afb642f64b1c17a6.png)

已重定向至页面。

![](img/cb9d43b7c9f2c24880d4866939eaad12.png)

包含“丽贝卡”的名字。看起来很奇怪。对我们有什么用？以后会有用的。我们最终会发现的。

我们将继续从前面的大厦地图中获取其他目录。地图上的下一个是 diningRoom2F。

> 导航到 http:// <target_ip>/diningRoom2F</target_ip>

![](img/165862a435a9087b00baf7b2746b7f0d.png)

检查源代码。[Ctrl+U]

![](img/a8f1845532b095098ae0d9d14fe7d7ea.png)

有一个散列，我们需要解码它。看起来字符被旋转，因此 ROT13。我们再去网吧吧。

![](img/5c846e133549521a43036f7c05d8437c.png)

事实上，这种杂烩很糟糕。一旦打开它，我们会看到一条消息，说蓝色宝石在底层，这是餐厅和一个 html 页面访问。

> 导航到 http:// <target_ip>/diningRoom</target_ip>

![](img/1a1df40712e80efb6ecb986cb27335a5.png)

我们将输入 gold _ 徽标志，然后单击提交。

![](img/6844094cd697f89df330d0865e744218.png)

我们得到了一个散列，我们需要解码它。它看起来又变坏了，但可能是密文。我们先用密码分析器怎么样。我识别密文的方法是:

[](https://www.boxentriq.com/code-breaking/cipher-identifier) [## 密码标识符(在线工具)| Boxentriq

### 被密码或密文困住了？这个工具将帮助您识别密码的类型，并为您提供信息…

www.boxentriq.com](https://www.boxentriq.com/code-breaking/cipher-identifier) ![](img/7269835eb6e16cdd14e010476f94602a.png)

的确是。散列是 Vigenere 密码编码的，要解码它，我们需要一个密钥。回想之前我们想知道“丽贝卡”这个名字对我们有什么帮助。我们可以用“丽贝卡”作为密钥。赛博咖啡馆会马上破解它。

![](img/3a90ff47e0267726060fd853cc1fbe50.png)

我们发现了一个 html 页面，它将包含盾牌标志。让我们抓住它。

![](img/25f449b1051748c7af29e01f43cc45e1.png)

太棒了。！提交吧。

> #5.什么是盾键标志
> Ans:shield _ key { xxxxxxxxxxxxxxxxxxxxxxxxxxxx }

回想一下，我们也有一个 html 页面要访问，但我们没有；直到现在我才参观过它。

> 导航到 http://<target_ip>/dining room/xxxxxxx . html</target_ip>

![](img/3512ea1d05fa50f92946ae2d8f1d18fe.png)

干得好！！这是蓝色宝石旗。提交吧。

> #6.什么是蓝色宝石旗
> Ans:blue _ jewel { xxxxxxxxxxxxxxxxxxxxxxx }

我们将跳转到大厦地图中的下一个目录，它是老虎状态室

> 导航到 http:// <target_ip>/tigerStatusRoom</target_ip>

![](img/4f5db5fe8d78b52911b582df8ca41c63.png)

我们必须输入一个宝石标志，但哪一个。
检查源代码。[Ctrl+U]

![](img/f474a46bf2e4ea3058d61121811b61ee.png)

看起来很好。让我们输入最近获得的宝石，即蓝色宝石。输入蓝色宝石标志并点击提交。

![](img/be96df18e4dd92baadf6dcca38603f50.png)

哇哦。！我们必须找到 4 个波峰，然后将它们组合在一起，得到一个基本的编码哈希并解码。当我说这将是一部史诗时，我并没有开玩笑。相信我，我很享受在这个盒子上的每一分钟。

我们有 crest 1 信息可以使用。永远记住破解时提供给我们的提示。Crest 1 哈希解码两次，包含 14 个字母。

但我们不会立即开始破解它们，让我们先收集所有的 4 个波峰，然后粉碎它们。

我们将进一步列举大厦地图上的其他目录，下一个是画廊。

> 导航到 http:// <target_ip>/galleryRoom</target_ip>

![](img/8b13702b0de38b0ea06bd3f26b2f75ff.png)

检查源代码。[Ctrl+U]

![](img/a71e3c775f7136d9f1f4734bafb0b28a.png)

看起来很好。让我们来看看这张纸条。

![](img/207432b2a38a0b0d33a74a214c592a3f.png)

精彩！！我们得到了 crest 2 的信息。请注意，提示已经更改。保持拉环和波峰 1 打开。我们需要剩下的两个波峰。

向前移动到地图上的下一个位置，那就是/自习室

> 导航至 http:// <target_ip>/studyRoom</target_ip>

![](img/8176ee588dbc3cb7b66a5acda90a8b29.png)

检查源代码。[Ctrl+U]

![](img/135a34ef35550538b6e9c06a2f6c5bfd.png)

看起来很好。
我们需要一面头盔旗来输入，但我们没有，因此我们将继续前进。

让我们看看地图上的下一个目录，那是军械库。

> 导航到 http://<target_ip>/armour room</target_ip>

![](img/7efc0b23e0c68a4856e8c7eb244e9cdf.png)

检查源代码。[Ctrl+U]

![](img/64f2907ab58b507f9b11aae109a8d4fc.png)

看起来很好。
我们必须输入已经捕获的盾牌标志。输入并点击提交。

> 已重定向至 http://<target_ip>/armor room xxxxxxxxxxxxxxx/</target_ip>

![](img/4c36afb09a6f5a704520bc01309759b7.png)

检查源代码。[Ctrl+U]

![](img/1f2c1843dc51790de567cd235b3f9d05.png)

看起来很好。
我们会看纸条。

![](img/4e3107904f6ff599614ba31198643efd.png)

这是波峰 3。太喜欢这个房间了。我们还需要一顶。保持这个标签打开，让我们移动到大厦地图的最后一个目录，那就是阁楼。

> 导航到 http:// <target_ip>/attic</target_ip>

![](img/d9e09412b244ac6c2b0b0878b1f605d2.png)

我们需要输入盾牌标志并点击提交。

> 已重定向至 http://<target_ip>/atticXXXXXXXXXXXXXX</target_ip>

![](img/aefe998cb18eccd8c5b4abad692c3734.png)

检查源代码。[Ctrl+U]

![](img/1e9d095880695aa56e4eb77e97384409.png)

看起来很好。
我们会看纸条。

![](img/348ca3b4ad58c5c34ca132b54de1b933.png)

我们已经成功地获得了所有的徽章。现在我们将开始逐一破解它们。从波峰 1 开始。

## 波峰 1:

> s 0 pxrkvvs 0 p xxxxxxxxxxxxxxxxxx

识别哈希:

![](img/8b5882489f7f47c0c4729129e377e248.png)

解码哈希两次:

![](img/2efb613b93f673d9a6c198c2d6e26e2d.png)

哈希 1- Base64
哈希 2- Base32

**RlRQIXXXXXXXX**仔细看长度正是我们需要的 14 个字符。

— —已完成的波峰 1

## 波峰 2:

> gvfwkxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

![](img/12d6945c23c7170891e1374dc8098c9a.png)

解码哈希两次:

![](img/8d1c4c1e86303f7daafc00d760ee83cc.png)

哈希 1- Base32
哈希 2- Base58

**h1bnRlXXXXXXXXXXX**
注意根据说明字符长度为 18 位。

— — —已完成的波峰 2

## 波峰 3:

> mdaxmtagmda xmtawmtegmdaxmtawmtegmdaxmtawmtegmdaxmtawmtegmdaxmtaxmtaxmtaxmtawmt awm xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx……..

识别哈希:

![](img/13ba636a05da07ad5bdfca2caa168337.png)

又是基本编码的。

对散列解码三次:

![](img/ffd16b3628c41f5595ca56afdb75f50e.png)

哈希 1- Base64
哈希 2-二进制
哈希 3-十六进制

**c3M6IHXXXXXXXXXX**
根据指令，字符长度为 19。

— — —波峰 3 完工

## 波峰 4:

> gsuer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

识别哈希:

![](img/5a07a0bd6afa92ab92132d55d51cba61.png)

还是 base64。

解码哈希两次:

![](img/1b34ea70fd44e1886d6662a428982677.png)

哈希 1- Base58
哈希 2- Hex

根据说明，字符长度为 17

— — —已完成的波峰 4

## 关键时刻:

根据说明，我们已经得到了散列，现在我们必须将它们组合起来。

波峰 1 +波峰 2 +波峰 3 +波峰 4

rlrqxxxxxxxxxxx+h1 bnrlxxxxxxxxxxxxxxx+C3 M6 ihxxxxxxxxxxxxxxxxx+pzgvfxxxxxxxxxxxxxxx

我们挖了这么久的大麻是

> **rlrqxxxxxxxxxx h1 bnr xxxxxxxxxxxxc3 M6 ih xxxxxxxxxxxxxxxxxxxxxxxxxpzgvfxxxxxxxxxxxxxxx**

太棒了。！我们现在将对它进行解码，并希望得到下一个标志的答案。

![](img/ff954bf42c157820a0e0929123d35c18.png)

太好了。！那太有趣了。我们已经获得了 FTP 证书。提交它们。

> #7.什么是 FTP 用户名
> Ans: XXXXXX
> 
> #8.什么是 FTP 密码
> Ans:xxxxxxxxxxxxxxxx

终于，我们离开了豪宅。我们要去警卫室。

## 任务 3-警卫室:

![](img/eac48718198137b40a55fab441a28045.png)

任务 3 列表

我们将使用 FTP 凭据作为用户。

![](img/1232e754afc123a790e8c969b67ec825.png)

我们会尽一切努力查看文件。

![](img/a368b742496247d76ccb3d2ae0711f01.png)

我们将使用“mget *”将所有文件传输到主机。

![](img/e407e0901229372d08ae644724946a3f.png)

永远记得在离开 FTP 前使用“ls -la”检查隐藏文件。

![](img/d63dd12ebdd446d435a7d87b92422fbc.png)

退出 FTP，让我们逐个检查传输的文件。

![](img/f390fef5d6b5840588176ce2a9e7910a.png)

> 重要. txt

![](img/2be0222ce83ee0ba4f86957014e33eb3.png)

头盔密钥在我们需要首先解密的文本文件中，以查看其内容。不管怎样，我们找到了一个目录和任务的答案。提交吧。

> #1.巴里
> Ans: /hidden_closet/
> 
> 头盔 _ 钥匙. txt.gpg

![](img/2fc41b19183b638eec360437925f523c.png)

要获得头盔密钥，我们必须输入密钥或密码短语。会是什么呢？我们迟早会找到它的。

> 001-key.jpg

![](img/0e797cbfbac520cc9f9b4dd1881d5b1e.png)

> 002-key.jpg

![](img/97a72e3f44121947db24ce774f82ae2f.png)

> 003-key.jpg

![](img/3374f6e01b70bde8955a5b3a1da99414.png)

三个关键图像 001、002、003 是。jpg 并可能包含隐藏信息。我们需要使用隐写术工具提取信息。

> 导航到 http:// <target_ip>/hidden_closet</target_ip>

![](img/8952e77f9a1046b0e357d171b575695d.png)

门是锁着的，需要输入头盔旗。如果你记得我们有更多的目录需要头盔旗。因此我们还没有完全列举出来。

我的首选 steg 工具是 steghide 和 exiftool。如果您还没有安装它们，请继续。

> apt-get 安装 steghide
> apt-get 安装 exiftool

要使用 steghide 提取信息或文件:

> 隐写提取物-sf<image_file></image_file>

![](img/4a9eb1ccc46db05488be0b6469fc5730.png)

出现密码提示时，按 enter 键，文件将被提取出来。

![](img/1ce50d92c2cc1a8c3fd120ea65d63ac2.png)

厉害！！我们有一份杂烩。让我们把那个留着以后参考。

cgxhbnxxxxxxxx

——steged 001-key . txt

使用 exiftool 提取元数据:

> 退出工具<image_name></image_name>

![](img/66149e7a093593bbf0092b3611317d84.png)

我们在注释字段中有一个散列。

5fYmVfXXXXXXXXX

——002-key.jpg 出口

我们将在第三个关键图像文件上再次使用 exiftool。

![](img/6ac47411375dc8994a544c64761c48d5.png)

图像通过 jpeg 再压缩进行压缩。我们将不得不使用“binwalk”命令解压缩它。Binwalk 是一个提取工具，可以从固件映像中提取嵌入式文件系统。

> 宾沃克-e<image_file></image_file>

![](img/161f5440cb33c0c611a1bc17b759cde8.png)

让我们“ls”到提取的目录中，并“cat”密钥-003.txt

![](img/dad170bb59b6a89ca149cf15e19d2213.png)![](img/c4de853f905ceac67025e09d907c3bc6.png)

3 axroxxxxxxxxx

———宾沃德·003-key.jpg

我们总共有 3 个哈希，这意味着我们必须将它们组合在一起并解码。

cgxhbnxxxxxxxx 5 fymvfxxxxxxxxx3 axroxxxxxx

我们可以去 Cyberchef，它会把工作做得天衣无缝。

![](img/a58903cd3e75bc97ba93446a48badf1c.png)

哈希是 base64 编码的，我们已经获得了密码短语和任务的答案。提交吧。

> #2.加密文件
> 的密码 Ans:plant xxxxxxxxxxxxxxxxxxxx jolt

这将让我们解密 helmet_key.txt.gpg。粘贴密码并单击确定。

![](img/14b9166b2af3e8099fb176b0f5f88e9e.png)![](img/cf6c05e009ca92b67e5d5326c3ada378.png)

太棒了。！让我们抓住头盔钥匙旗。

![](img/3a03a0daf2263a5bff4ea9dbf3ddebe6.png)

精彩！！我们终于获得了头盔钥匙旗和任务的答案。提交吧。

> #3.什么是头盔钥匙标志
> Ans:头盔钥匙{ XXXXXXXXXXXXXXXXXXXXXXX }

我们将需要重新访问被锁定的目录/页面，并需要头盔钥匙标志解锁。

如果你忘了那些是什么，我会把它们写下来让你记住:
/自习室
/隐藏的壁橱

## 任务 4-重访:

![](img/c6b6724680687847a803efc6ae2fdad0.png)

导航至 http:// <target_ip>/studyRoom</target_ip>

![](img/485f1f3fcf4174106552435907c491c2.png)

输入头盔钥匙标志并点击提交。

![](img/46fca6cf1c693143abbba44da874a344.png)

检查源代码。[Ctrl+U]

![](img/f05a34b2ab694a5e2465dfddcdd9465f.png)

看起来很好。让我们来看看这本书。单击“检查”,我们将下载/保存文件“doom.tar.gz”

![](img/ec5e72017c8a56c694cc479014fc953c.png)

我们需要解压缩文件。

![](img/c34afdba49465a929c58cb34f412fd97.png)

> tar -xf<file_name></file_name>

![](img/f1f93db016c1a2156b30aaa16cbd0c34.png)

该文件提取了“eagle_medal.txt”文件，我们将对其进行“编目”。

![](img/1879276af90c8a7ad380e88054b1bfe1.png)

太棒了！！我们得到了 SSH 用户，现在我们需要 SSH 密码。然而，这回答了这个问题。提交吧。

> #1.SSH 登录用户名是什么
> Ans: XXXXXXXXXXXXXX

我们还有一个目录要检查，那就是/hidden_closet。

> 导航到 http:// <target_ip>/hidden_closet</target_ip>

![](img/aa3a003b836c25d65d8c3c1a5c42cf25.png)

输入头盔钥匙标志并点击提交。

> 已重定向至 http://<target_ip>/hiddenclosetxxxxxxxxxxxxxxxxxxxxx</target_ip>

![](img/04cc1872219b2a9b7b2bb5e30d97f813.png)

检查源代码。[Ctrl+U]

![](img/ad8fe93e140f7803d9130ca44d0efade.png)

看起来很好。
我们先读取 MO 盘 1 文件。

![](img/537eabec46c164e050f1feb45d9c7be2.png)

有一个散列，看起来字符被旋转，这表明它可能是 ROT13 编码，但它也可能是密文。我们可以先尝试用以下方法进行分析:

[](https://www.boxentriq.com/code-breaking/cipher-identifier) [## 密码标识符(在线工具)| Boxentriq

### 被密码或密文困住了？这个工具将帮助您识别密码的类型，并为您提供信息…

www.boxentriq.com](https://www.boxentriq.com/code-breaking/cipher-identifier) ![](img/d91c04c09eb3d87a351c1e7d4423434a.png)

它确实是密文，已经被鉴定为博福特密码。密文的问题是当你想解码它们时，它们需要一个密钥。换句话说，这意味着这个散列完成了 50%的工作，但不是 100%。我们仍然需要密钥来成功解码哈希，就像我们之前使用“rebecca”作为密钥来解码为我们提供 FTP 凭据的哈希一样。

让我们检查另一个文件。我们会检查狼勋章。单击检查。

![](img/8ef3bb4c98de45c3973d827038cba118.png)

耶！！我们之前已经获得了密码和用户名，因此我们可以 SSH 到机器中并获得一个 shell。提交任务的答案。

> #2.SSH 登录密码是什么
> Ans: XXXXXXXXXXX

在做 SSH 之前，我们还有一个任务要回答。这很简单。你可能以前读过。

![](img/22b0488f43b469b988e5df8890a59edf.png)

提交吧。

> #3.bravo 团队领导
> Ans: Enrico

## 任务 5-地下实验室:

![](img/2588cf92859cafddf89a0f36f2736f6f.png)

任务 5 列表

现在闭嘴😃

![](img/a25496b392120d9d1cd1002ad8469701.png)

我的 TryHackMe 机器过期了，所以我不得不重新启动它。IP 变了，没别的了。

我们是一家人！！
我们正在粉碎它！！
我们将执行“ls”来查看用户主目录中的文件。

![](img/8affa6c92b36d8934d283b4826420d20.png)

。jailcell 的目录看起来很有趣。让我们开始吧。

![](img/fcccf6bb0957f3b46b3022b23a0635ae.png)

有一个“chris.txt”文件，其中包含大量信息和标志。我们可以边走边提交。

> #1.你在哪里找到克里斯
> 答:监狱
> 
> #2.谁是叛徒

有趣的是，在底部，我们可以看到 MO disk 2: albert
它可能是密钥，我们可以尝试解码 MO disk 1 文件中的密文。

为此我们可以去赛博咖啡馆。由于 cyberchef 不支持博福特密码，我们仍然可以通过选择 Vigenere 密码解码。它会像魔咒一样管用。

![](img/e625557442ec11f486036ab635189232.png)

太棒了！！我们已经获得了用户“weasker”和密码“xxxxxxxxxxxxxxxxxxxxxxxx”

让我们简单地使用“su weasker”作为 weasker 登录

![](img/bca8566f879f69f21f9984d58ea3f01c.png)

韦斯克

我们以用户“weasker”的身份登录。现在我们需要检查他主目录中的文件。

![](img/eaf46bbcdec91fadf137f1851615055f.png)

有一张纸条。我们会检查的。

![](img/34d37072c196fd51e62ed820a27c8b48.png)

笔记提到了终极生命形式，暴君，这就是任务的答案。提交吧。

> #4.终极形态的名称
> Ans:暴君

最后一面旗，也是最重要的一面旗，根旗被留下来搜寻。我们在机器上拥有升级权限，可以成为 root 用户并捕获 root 标志。

我们将检查可以由用户“weasker”作为根用户执行的命令或二进制文件。

![](img/693e8c4d7ffb2c66b197e61f57511e44.png)

太棒了。！Weasker 可以以 root 身份执行任何命令或二进制文件。

我们可以简单地“sudo su ”,我们将成为 root。

![](img/230d629c3109cb68cf05cc39e9ef55b0.png)

超级牛逼！！我们现在可以确认我们是根了。我们已经成功升级了权限。

现在拿到根旗，给这场噩梦一个美好的结局。

![](img/837e5400bb65bc8f322613ebd93cbdc1.png)

提交吧。

> #5.根标志
> Ans:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

![](img/1f4d3d8031605cdff2b7cb31a64e905d.png)

恭喜你。！完成了房间！！这是一个超级史诗般的房间，我写得超级详细。我会建议每个人都试试这个盒子，你将能够测试你的技能，以及提高或改善你的许多不同的技能，如枚举，哈希破解等。

如果你喜欢这篇文章，并且这篇文章对你有所帮助，请在评论中告诉我，或者用掌声分享你的爱。

谢谢你抽出时间。

跟着我。

更多的报道正在进行中。

保重，注意安全，继续黑！

**-哈桑·谢赫**