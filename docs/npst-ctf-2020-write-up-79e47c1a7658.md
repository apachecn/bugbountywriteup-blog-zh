# NPST CTF 2020 —报道

> 原文：<https://infosecwriteups.com/npst-ctf-2020-write-up-79e47c1a7658?source=collection_archive---------1----------------------->

## PST 制作的 2020 CTF 圣诞挑战日历。

![](img/1b5d2c3ca3eebfe76748b95bd6e460e2.png)

文字说明，来自 [npst.no](http://npst.no) 的屏幕打印——作者和 [freepik](https://www.freepik.com/premium-vector/anonymous-hacker-concept-with-flat-design_2785048.htm#page=2&query=hacking&position=3)

这是 NPST 为【2020 年 CTF 圣诞挑战写的一篇文章。下面这篇文章会陪你走过每一天。所有用到的文件都可以在这里[下载](https://knoph.cc/ctf/write-up/NPST2020.zip)，所以你可以自己尝试一下。

2019 年，被称为挪威安全警察的 PST(Politiets sikkerhets tje neste)决定发布一些广告，通知人们他们在这个圣诞节寻求帮助。北极安全局需要一些优秀的精灵侦探的帮助。圣诞节危在旦夕，只有我们能拯救圣诞节。至少这是游戏的情节。这是如此巨大的成功，以至于他们决定每年举办一次。

圣诞节倒计时的每一天，都被赋予了新的任务。每解决一个任务，你可以得到 10 分。当然，还有一些复活节彩蛋。复活节彩蛋不会给你任何分数，但如果有人拥有相同的分数，它就可以打破僵局。谁不喜欢这些额外的小惊喜呢。整个 CTF 充满了内部笑话，并有一个伟大的圣诞主题，我建议明年。

如果你在寻找 2021 年的报道，你能在这里找到吗:

[](/npst-ctf-2021-write-up-96b58464151d) [## NPST CTF 2021 —报道

### PST 制作的 2021 年 CTF 圣诞挑战日历详细演练。

infosecwriteups.com](/npst-ctf-2021-write-up-96b58464151d) 

如果没有，让我们开始写这篇文章吧！

# 入门指南

首先，我们来谈谈这里的游戏玩法。PST 在为所有玩家提供操作环境方面做得很好。当你第一次进入这个网站时，你会看到一个窗口告诉你游戏已经打开。

![](img/175c97591b17f013bd08eabe71a6a0bc.png)

从 [npst.no](http://npst.no) 检索到的 NPST 开始屏幕—作者

一旦你进去了，你知道吗？它是一个桌面！他们已经开发了你自己的带有邮件收件箱的桌面，等等。确保你会感到受欢迎！

![](img/6c4af6e11380edc138d5ad4056685c43.png)

DASS 操作系统的桌面，从 [npst.no](http://npst.no) 检索—作者

当点击开始菜单时，它会弹出一个很棒的菜单选项。Snabel-A 是用于处理挑战和发送答案的邮件系统。它还可以作为与游戏创作者的交流系统。你也有一个记分牌，信息，登录和一个功能称为 Forbedre(增强)。我们稍后将对此进行探讨。但是首先我们需要登录。

![](img/9b138b4ddc476f86573727dbe172828f.png)

DASS 操作系统中的开始菜单摘自 [npst.no](http://npst.no) —作者

首先打开开始菜单，点击登录并选择你想在游戏中使用的账号。

![](img/745b8b5edb458de317e020a7c9b31685.png)

从 [npst.no](http://npst.no) 检索到的登录信息—作者

并非菜单中的所有选项都可供您使用。总而言之，这个游戏有很大的潜力。例如，在我们开始之前，让我给你看一个简单的东西。在游戏过程中，你有一个记分牌。

![](img/b2d15d2f9ced7c94c89ded78e0044004.png)

记分牌检索自 [npst.no](http://npst.no) —作者

在“显示(vis)”下，有一个名为“显示所有标志”的选项。如果你点击那个，你会看到一个带有二维码的蓝屏。猜猜这有什么用！你可以亲自尝试一下。创作者在剧中隐藏了许多小笑话。这使得游戏变得更加有趣。

![](img/9f5bd02fea79ad138f3fe077d437fd0e.png)

从 [npst.no](http://npst.no) 检索的记分牌蓝屏—作者

# 第 1 天—塞萨尔密码

![](img/960408dcae8549f2026b5193807b9256.png)

第 1 天—确认您的 ID。检索自 [npst.no](http://npst.no) —作者

游戏以一个很棒的笑话开始！您会收到一条消息，告诉您需要验证您的帐户。与您开立的所有账户一样。他们甚至会给你提示。他们把信息放在沙拉里。这是凯撒沙拉的暗示，指的是凯撒密码。

快速暴力破解按键:**RUV { jgkjqpgtfgvlwnkilgp }**on[cyber chef](https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,-2)&input=UlVWe0pna0pxUOVHdEZndkx3bktpbGdwfSA)向您展示旋转字母两次，您会得到有意义的单词:**PST { heihon ErDetJulIgjen }**

> 第一天:PST { heihonerdetjuligjen }
> -启动圣诞节

# 复活节彩蛋# 1——藏在视线之内

这是第二个被发现的蛋。3 号复活节彩蛋是人们找到的第一个彩蛋。每个人都认为这将在游戏的后期出现。

我是游戏中第一个发现它的人，它确实在 discord 频道引发了一场理论爆炸。我在哪找到的，是 1 号还是 2 号。我和在 Discord 频道上认识的 Mats 一起搜索。我们上下搜索网页寻找线索。

这是因为我们确实设法找到了 3 号，因此必须有 1 号或 2 号。但是够了。复活节彩蛋#1 更像是一个测试钢笔的笑话。每个人都知道你在网页上首先检查的是 robot.txt。

这没有给我们任何好的信息。不过开个玩笑， **robots.txt** 的反义词是什么？嗯当然 **humans.txt** 。这个文本文档在顶部包含一些信息。很多很多行在底部是复活节彩蛋。

> 彩蛋 1:彩蛋{ sh 4 rks _ d0t _ txt }
> ——找人类？

# 第 2 天— MIDI 转换

![](img/39fe4f9d6c99897f46b1e3ac991a7495.png)

第 2 天—从 [npst.no](http://npst.no) 中检索 MIDI 转换—作者

他们以某人被捕的故事开始游戏。他们在 midi 中找到了一个声音文件，还有一个私有的. 7z 文件。要打开这个. 7z 文件是不可能的。它的密码保护和暴力迫使它需要太长时间。最好的猜测是，这是另一天。所以，让我们把重点放在 midi 文件上。

![](img/a597555c1a8640c877d2f6af22bf6751.png)

当你打开它，你可以看到它是一个普通的 midi 文件。它包含一个乐器，和这个乐器的演奏音符。如果你阅读关于 midi 文件[伟大的关于 midi](https://www.audiolabs-erlangen.de/resources/MIR/FMP/C1/C1S2_MIDI.html) 的页面，你会发现每个音符都有一个音高，开始和结束。这里有趣的是音高。如果我们把它转换成 ascii 会发生什么？我们可以阅读的信件。

我打开 visual studio 代码，创建了一个 python 脚本。它读取所有的音高，并将其转换成 ascii 码，然后呈现给我:

输出的结果是:60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 83 84 123 66 97 98 121 80 101 110 71 119 121 110 68 117 104 68 117 104 68 117 104 68 117 104 68 117

**音符转换:**

<=>@ ABCDEFGHIJKLMNOPQRSPST { babypengwynduhduhduhduhduh } TSRQPONMLKJIHGFEDCBA @？>=

> 第二天:PST { babypengwynduhduhuduhuduh }
> -圣诞欢歌

# 第 3 天—图像 Stegano

![](img/a12a4e19212d7da0f08c9064988a9ec2.png)

第三天——破解从 [npst.no](http://npst.no) 取回的纸杯蛋糕——作者

昨天晚上，圣诞老人的一名雇员设法猜出了我们昨天得到的 zip 文件的密码。此外，它们还为我们提供了一条线索，告诉我们很有可能隐藏着一些你无法亲眼看到的东西。

![](img/374e0c010017811807a0068800045a42.png)

从 [npst.no](http://npst.no) 中检索到的第 3 天文件的密码(作者)

打开 privat.7z 文件是一个小小的挑战。因为这条消息告诉我们:**最终，我猜出了 zip 文件的密码，并且成功了！从我的 PDA 发送。**

在这种情况下，密码是: **til zip-fila** ，(对于 zip 文件，)。打开它，我们发现两个项目一个 txt 文件，和一个图像。这个文本文件就像你期待的复活节彩蛋一样，但是此时我们还没有工具来弄清楚它。如果你想掌握这个秘密，你可以跳到复活节彩蛋#2。

![](img/c98e2fcb81faaa78866f531942adf266.png)

从 [npst.no](http://npst.no) 检索的图像和文件目录—作者

Cupcake.png 向我们展示了一只企鹅在阳光下悠闲地吃着松饼。我认为这个挑战是最难的。在得到它之前，我尝试了几乎所有的方法！当我得到答案时，我从未如此沮丧过。所以，让我们演练一下这个挑战。

我打开文本文件，思考着这到底是怎么回事？这没有任何意义，看起来也不像是熟悉的东西。我稍后会关注这一点，然后再看图像。

我首先在图像上运行 [zteg](github.com/zed-0xff/zsteg) ，看看是否有隐藏的东西。

幸运的是，这张图片包含一个链接:[youtu.be/I_8ZH1Ggjk0](http://youtu.be/I_8ZH1Ggjk0)。这是一个来自中情局的视频链接，演示了他们放大和增强图像以感知背景中的一个小东西。自然，这是一个巨大的暗示！因此，我开始放大，检查你命名的颜色层。投资几个小时浪费我的时间。

那时，当我突然去查看 dass.npst.no 的收件箱时，我找到了解决方案。我不敢相信这如此简单。菜单中有一个名为 Forbedre(增强)的选项。我之前上传了一些图片来测试它，但是它总是回复错误的图片。因此，如果这一次是正确的图像呢？

![](img/687e33cdf3b7599a75bc1f75dcdb37a3.png)

从 [npst.no](http://npst.no) 检索的菜单—作者

我上传了图片，然后点击了 Forbedre(增强)。我一按下按钮，图像就开始放大，每按一次按钮，图像就会变得越来越好。一路往回走，这样我就能在最后读到贴在棕榈树上的帖子了。

![](img/94376f6406ca48f699efc64ede73b251.png)

缩放以获取从 [npst.no](http://npst.no) 检索的标志—作者

第三天:PST{HuskMeteren} —提醒保持一米社交距离。

# 复活节彩蛋 3 号——斯蒂加诺

这是第一个被发现的蛋。我们还没有找到蛋#1 和蛋#2，此时我们无法解码。想知道图像中是否隐藏了什么，我们从增强器中得到我用 zsteg 再次运行所有的图像。最后一张图给了我这个结果。

> EGG 3:EGG { measureoncecutwice }
> -检查两遍？

# 第 4 天— MSSQL 错误代码

![](img/9db256ebb6297cd244750a7e9aa036ee.png)

第 4 天—从 [npst.no](http://npst.no) 检索 MSSQL 编码—作者

我们醒来时收到一封信，告诉我们人事系统有问题。因为圣诞节总是在复活节结束，他们是不是在计算离复活节还有多长时间之类的。不管怎样，剧本有些问题。他们认为我们可以找到正确的“MaalTall”并把它送回去。

![](img/44f3584635b53ca60d165ef63aea2a14.png)

从第 4 天开始下载的文件列表—按作者

![](img/99d0769a1a192c675e43d50508a1fc7c.png)

查看 Excel 文档—按作者

我们接收所有文件和 MSSQL 脚本生成的 csv 文件。我没有安装 MSSQL 数据库。也没有安装的计划。所以，我看了文件。我发现生成复活节日期的公式有问题，我找到了生成复活节日期的函数。创建一个 python 脚本，给我正确的日期并添加数字。巫婆导致了一天的旗帜。

> 第四天:太平洋标准时间{999159}
> -复活节前的工作日

# 第五天——奇怪的日志

![](img/8ae50c897a1d4878bd55ce5d9ff8e36e.png)

第五天—从 [npst.no](http://npst.no) 找到丢失的图片—作者

有一些不寻常的活动，他们追踪到了安全部。他们向我们提供了他们认为安全漏洞发生时的日志。我们的工作就是调查此事。

![](img/31a32b7d9c7f27da38016a49a2738764.png)

密码日志的副本—按作者

手动浏览需要一些时间。因此，我也可以创建一个 python 脚本来查看它。想看看是否有什么异常。

我从组合名字开始，它们是否被不止一次地使用，它们在主题上的不同。什么样的密码被替换，被替换成什么。

这个脚本给了我一个输出，我发现有一个名字没有被使用超过一次。所以，它也必须是今天的旗帜。

> 第 5 天:PST { 879502 f 267 ce 7b 9913 C1 D1 cf 0 acaf 045 }
> -干草垛中的针

# 复活节彩蛋# 4——周末休息

![](img/b19eface096590c7abf44c2c4722c833.png)

从 [npst.no](http://npst.no) 检索到的告诉我们周末不要工作的消息—作者

今天我们登录电脑(dass.npst.no)时，是不是无法打开邮箱了。有一个叫 AMIA(防止周末工作组织)的团体，他们在周末禁用了邮箱。

![](img/3e5fba0af176b23c83777cb25a98729f.png)

将时钟拨回从 [npst.no](http://npst.no) 检索—作者

解决方法是将计算机(dass.npst.no)上的时钟向前或向后拨，这样就不再是周末了。通过这样做，我们能够获得计算机，并阅读来自“我们的老板”的新邮件，通知我们通过电子邮件发送复活节彩蛋代码来认可他正在做的工作。这给了我们 4 个蛋。

![](img/cfb35ef08cc664b5f63bba1f1292cfb2.png)

鸡蛋 4——说点好听的从 [npst.no](http://npst.no) 检索——作者

> 彩蛋 4:彩蛋{ w0 rlds _ b3st _ b0ss }
> ——永远给你老板好评。

# 第 6 天—雪橇介绍 8

![](img/4cb3722d3a5f27f7b13748ec958b6581.png)

第 6 天——从 [npst.no](http://npst.no) 检索到的 SLEDE 8 简介——作者

这个太棒了！PST 为这个游戏创建了一个汇编模拟器。他们称之为雪橇 8 号。这是一个功能齐全的汇编编辑器。今天的游戏就是要抓住它。你可以在 [slede8.npst.no](http://slede8.npst.no/) 访问它，如果他们没有撤回它，你可以在 [PST GitHub](https://github.com/PSTNorge/slede8) 找到源代码。我们还收到了 [README](https://github.com/PSTNorge/slede8/blob/main/README.md) 文件！

![](img/06752ad77128a4089cf1df46b45da049.png)

Slede8 IDE 检索自 [slede8.npst.no](http://slede8.npst.no) —作者

我们收到的代码打开了一个训练模块。通过完成任务，将它分派给服务器，我们获得了今天的密钥。该程序的工作方式很简单，它从联邦调查局读取。并且可以用十六进制写对口型。

我们今天的任务是读取第一个字节，它告诉我们字符串有多长，然后一起计算下面的数字。这是我的解决方案。

> 第六天:PST{ ATastyByteOfSled}
> -组装圣诞版介绍。

# 复活节彩蛋#2 — Kladd.txt

你还记得之前的文本文档吗？那是一个网址！您可以将预编码的消息加载到 slede-8 编辑器中。文件上面的 8 个雪橇就是暗示。所以，在你的浏览器中输入这个你得到了下面的代码，运行这个代码你得到了第二个复活节彩蛋！

[https://sled E8 . npst . no/# n 4 igzg 9 grgtgxguwmiqcyjalhazqkiaqbabdaiwa 0x ada b 5 ka 6 adggo 4 dsakgginlxlm+iqpwuabae 4h 7 aeqcwo 5 gaka 8 gav 1 nwgcyezzgasatsczmadabaggrva ali4 p avle 0 eclwa 4 ADI 8 ai 3 f-mtpusjphxzoaqy 9 hrmiamy 9 jvmtbe 214 InP 3 VC 8 ans 94 sro-agym](https://slede8.npst.no/#N4Igzg9grgTgxgUwMIQCYJALhAZQKIAqBABDAIwA0xADAB5kA6AdgGo4DSAkgGInlXlm+IqWpUAbAE4h7AEqcWo5gAkA8gAV1NWgCYEzZgAsAtsczMAdABEAggRvaALI4p1xAVle0ECLwA4Adi8AI3F-MTpUSjpHXzoAQy9HRMiAMy9JVMtbe214iNp3VC8AnS94sro-AGYMuNpJTzoEJtpUAtQwhJSGnWy7BzoA+tS4ENq6auCQ9Lp3aO9phPqEYqrK+jG6HUcDJnUAGQBVHBxRAUYmDnlFGGoZG6Urwj4xbQDgh4UngiPZYjAAE9UAAHCAAGwQTGYhxOZzuFy+t3u0KuBA06j2QNBEKh5meInILiewj4ZE8pEuzG4nAAcrTiCZjMwABp4A4HPD-GBlUh9Al8Cl0KlMCDGADWADdVvjOfDqswDjYcHxdkw2RyuaRaqQ1YrOOw+QIFUwAEJqTTEMFgAAu4KgqAAllBmVc5N8YCbYadzqR3CoMcQxVLVntrXaHc6zEIXr6iTHCZRKf6mLJCH89swQBQQI6mCCoDasCAyKVJPFgnBqqkpn5JI5quJxMEdAF4qka3B5sEyKh2u1EHBm2RJJJgn50KkAtQ4JIdKgdO5hhUdH4fGQaj4PDsQABfIA)

> EGG 2:EGG { sled E8 exampleforpstinternaluseonly }
> ——千万不要跳过教程

# 复活节彩蛋#5 —雪橇 8 教程

打开 [SLEDE-8](http://slede8.npst.no/) 软件，你可以输入一个代码或者在两个教程之间选择。如果你完成了所有的教程，你会得到一个鸡蛋。

这是我对两个教程的解决方案:

> 彩蛋 5:彩蛋{你好，雪橇 8！}
> ——你现在，知道组装了

# 第 7 天—复杂 16u

![](img/158b5f1adeeccd25b65fac246bd29b9f.png)

第 7 天—从 [npst.no](http://npst.no) 检索到的 Complex16U 作者

这不是我喜欢的。当打开今天的信息时，他们通知我们有一些奇怪的信号在流传。自然，他们得到了一份副本，并要求我们看一看。

在网上简单搜索后，我发现 [URH](https://github.com/jopohl/urh/releases) 可以阅读这类文件，并且分析得很好。我检索并安装它，然后打开文件。

![](img/50a74a06171a62afbaaa4c7c01b1db80.png)

URH 的解码——作者

它给我以下输出:

1010101010101010101010101010101000100000001000000101000001010011010101000111101100110000011011100101111100110000011001100110011001011111011010110011001101111001001100010110111001100111010111110011000101110011010111110011001100110100011100110111100100100001011111011

在[网吧](https://gchq.github.io/CyberChef/#recipe=From_Binary('Space')&input=MTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAwMDEwMDAwMDAwMTAwMDAwMDEwMTAwMDAwMTAxMDAxMTAxMDEwMTAwMDExMTEwMTEwMDExMDAwMDAxMTAxMTEwMDEwMTExMTEwMDExMDAwMDAxMTAwMTEwMDExMDAxMTAwMTAxMTExMTAxMTAxMDExMDAxMTAwMTEwMTExMTAwMTAwMTEwMDAxMDExMDExMTAwMTEwMDExMTAxMDExMTExMDAxMTAwMDEwMTExMDAxMTAxMDExMTExMDAxMTAwMTEwMDExMDEwMDAxMTEwMDExMDExMTEwMDEwMDEwMDAwMTAxMTExMTAxMQ)的一个快速转换给了我今天的旗帜。

> 第七天:PST{0n_0ff_k3y1ng_1s_34sy！}
> -complex 16u 简介

# 第 8 天— ASN1

![](img/2656f284f2d48f7c083ed1c617c4149c.png)

第 8 天—让我们学习从 [npst.no](http://npst.no) 检索到的 ASN1 作者

今天他们说我们需要发现新的东西，今天的主题是 ASN1。他们给了我们下面的代码，我们需要找出如何破译它。

我对 ASN1 做了一些研究，找到了一个可以读取 ASN1 文件的 python 库。编写一个快速代码并获得结果:

> 第八天:PST { ASN 1 ichooseyou }
> ——为什么不掺点口袋妖怪进去。

# 第九天——圣诞节

![](img/3ed06d73dfe64f0c4e61345381e93ec8.png)

第 9 天—作者从 [npst.no](http://npst.no) 中检索一些六边形的时间

这真是一件痛苦的事，我尝试了所有的方法，最终从一位参赛者那里得到了一个提示。我搜索每一个地方的表情转换器。按照提示将文本转换为十六进制。

🎅🤶❄⛄🎄🎁🕯🌟✨🔥🥣🎶🎆👼🦌🛷

🤶🛷✨🎶🎅✨🎅🎅🛷🤶🎄🔥🎆🦌🎁🛷🎅❄🛷🛷🎅🎶🎅✨🎅🦌🥣🔥🛷🦌⛄🎅🌟🛷🛷🔥🎄🦌🎅✨🦌🦌🕯🎶🎅🤶🦌❄🎁🕯🎅✨🎶👼🌟🎆🕯🌟❄👼🎅🎅🤶❄🎄👼🎆🔥🎁🛷🤶👼🎅🎅🎅🎅🎅🎅

但是什么都没有！什么都没用！这太令人沮丧了，但在得到暗示后，我开始数数。表情符号中的第一个字符串有 16 个字符长。这是每个表情符号中的一个…会这么简单吗？是啊！第一个表情符号是 00，接下来是 01，以此类推。

但这并不是结束。把字符串转换成十六进制后，得到了一个奇怪的字符串。经过快速的尝试和失败后，我发现它是 gzip。我把它解压缩了，然后就有了今天的密钥。这是我最后的 python 脚本。

> 第九天:PST{🧹🧹🎄🎅🎄🧹}
> -祝你圣诞快乐

# 复活节彩蛋# 6——和罗布一起画画

![](img/27f11358d13d1319b83d32b7cf4ca282.png)

我们从 [npst.no](http://npst.no) 中检索到了作者的绘画

在今天的任务中，我们更新了我们的 dass 电脑。油漆突然就有了。这使得我们可以使用自己的背景和更多。玩的时候，我们发现其中一个功能不工作。它提供了一个新的蓝屏。但是有些地方不一样。蓝屏源代码看起来确实可以运行。

![](img/6e3ade9cf36a7a5c9ccc2fa8f50326c9.png)

从 [npst.no](http://npst.no) 检索的新蓝屏—作者

我用的是 x86 汇编:[https://defuse.ca/online-x86-assembler.htm#disassembly](https://defuse.ca/online-x86-assembler.htm#disassembly)。这将返回一个十六进制字符串。在[网吧](https://gchq.github.io/CyberChef/#recipe=From_Hex('None')&input=NDU0NzQ3N0I3ODM4MzY1RjZENjE2MzY4Njk2RTQ1NUY2MzZGNjQ0NTcyN0Q)转换这个的时候，我得到了彩蛋的解决方案。

> 彩蛋 6:彩蛋{x86_machinE_codEr}
> -蓝屏并不总是让人头疼。

# 第 10 天—更多雪橇 8

![](img/575855c7578bbae61993509d9c956a10.png)

第 10 天—作者从 [npst.no](http://npst.no) 检索到更多 SLEDE8

新的一天，新的编程。今天的任务是发明一个代码，可以计算两个数字 A + B，并把它写成 ASCII 字符串。例如，A = 0xA0，B = 0x08 => '168 '。最初的任务没有超过 255 个大数字。但是完成后你会得到一个新的复活节彩蛋密码。同样是这个问题，但这一次可以一直到 512 及以上。

当提交您的代码时，它会在服务器上进行压力测试。他们尝试各种各样的数字来检查你的代码是否有效。这是我对鸡蛋和任务的解决方案。

> 第 10 天:PST { +++and kisses willbeawardedtoyou }
> -我的我的我的，你让我脸红
> 
> EGG 7:EGG { ba 92 AE 3 a9 af 1a 157703 ca 83d 9 a9 FB 11d }
> -大数十六进制加法

# 第 11 天— MD5 列表

![](img/5617949e1bdf453d796bee5c09c332c6.png)

第 11 天—从 [npst.no](http://npst.no) 检索到的奇怪列表—作者

NPST 的内部安全团队发现，坏孩子和好孩子的圣诞老人名单有一些非官方的修改。他们说 MD5 有一些变化，但他们不知道是哪一个。

![](img/21c0c824a0da2f5217ec7764ceac1768.png)

在 SQL 中找到的列表预览—按作者

我们得到一个包含所有名字和 MD5 的文件。我首先创建一个脚本，检查列表中是否有相同的名字。如果有人有相同的 MD5。然后我突然想到…他们是如何设法创建不同的 MD5 的，有模式吗？

快速尝试失败后，我意识到 MD5 是由名和姓组成的。我创建了一个脚本，它接受所有的名字并检查 MD5 代理，即列表中的那个。然后我发现一个 MD5 与名字不匹配。旗子被找到了。

> 第 11 天:PST { 49422712408d 5409 a 3 e 40945204314 e 6 }
> ——MD5 就是这个东西。

# 第 12 天— SLEDE8 反编译器

![](img/2e5db174fffa7b734eb16ffc094d202f.png)

第 12 天—反编译从 [npst.no](http://npst.no) 检索的 SLEDE8 作者

这个我经历了很多麻烦。我们得到了一个用 SLEDE8 编写的文件，我们的任务是调试它。我尝试了许多事情，但没有任何运气。是我的朋友 MATS 首先设法让它工作起来。他使用 slede8 节点资源编写了一个节点脚本，并暴力破解了标志。在这之后，我们得到了一个新的文件作为复活节彩蛋。我们没人能破解它。

> 第 12 天:PST { fib 0 NAC 1 _ 0 net 1m 3 _ p4d }
> -菲巴诺奇地穴

# 第 13 天—十六进制加密

![](img/d7980ce7ec3a1ef10bf616500e1805b0.png)

第 13 天—从 [npst.no](http://npst.no) 检索十六进制加密—作者

这太可怕了。我们收到一条信息，告诉我们他们收到了一份奇怪的传真。并且十六进制解码不产生任何意义。因此，我很自然地认为这是十六进制加密或什么的。我寻找了我所知道的每一种加密技术。各种各样的转变。但是什么都没有！

当时，我收到了一个提示，我不应该使用这些。事实证明。如果你只是从某个角度来看，似乎有一些文字隐藏在里面。您根本不需要执行任何操作。在我的屏幕上看到它是非常具有挑战性的，所以我拿出手机试图给屏幕拍照。就在那里！旗帜清晰可见，手机上的摄像头设法检测到文本中的小阴影，并清晰地显示出来。如果你想看的话，可以从顶部的下载 zip 中获得这个文件。

![](img/0066c1e51444dcf7a39d534dc6be286e.png)

从 [npst.no](http://npst.no) 检索到的鬼鬼祟祟的标志预览—作者

> 第 13 天:PST { SNEAKY _ FLAG _ IS _ SNEAKY }
> -该死的妖术。

# 第 14 天—另一辆雪橇 8

![](img/eaa99e65bcf4b6a195298b9d79885b10.png)

第 14 天—从 [npst.no](http://npst.no) 检索到的新一天新雪橇 8—作者

然而，另一个 SLEDE8 程序。我们得到了一系列数字。我们必须把它们反过来印。这次的彩蛋和第一个任务一样，只是绳子更长了。我在这两个任务中使用了相同的代码，它工作了。

> 第 14 天:PST { inreversecountryeverythiispossible }
> -逆转
> 
> 彩蛋 7:彩蛋{ 5 F5 fc 8819 e 2 cc 6 be 9 c6a 19370 a 5030 af }
> -新反向

# 第 15 天——完成 16u 并扭转

![](img/bdee37cbf3a8f4f9aa41561949df758e.png)

第 15 天—从 [npst.no](http://npst.no) 检索到的编译 16u—作者

这与第 7 天非常相似，它使用了相同的加密方法。但是有一个转折。我们今天得到的暗示是，信号是在去英国看与曼彻斯特的比赛后收到的。所以为什么不试试曼彻斯特二代加密呢？那起了作用。

![](img/67b56f107fc2262977cefba63e409486.png)

曼彻斯特解密 URH —作者

> 第 15 天:PST { m 4 nch 3 ST 3 r _ 3n c0d 1 ng _ 1s _ 4 _ l0t _ 0f _ fun！}
> ——曼彻斯特为胜

# 第 16 天—在雪橇 8 中分拣

![](img/81ec857aa06038cd206aa848c7b0ad56.png)

第 16 天—从 [npst.no](http://npst.no) 检索到的 SLEDE8 作者

另一个 SLEDE8 编程挑战。我们得到了一个字符串，我们需要排序运行发烧超过 5000 步。和往常一样，有一个新的任务有点难得到一个鸡蛋。鸡蛋挑战是相同的，但只有 4000 步。有点好笑的是，这个标志是一个指向气泡分类的 YouTube 链接。

> 第 16 天:PST{youtu.be/k4RRi_ntQc8}
> -泡泡排序为胜

# 第 17 天—ets 1232–1

![](img/1cae9fa68f553f42f17c1087c60b54ed.png)

第 17 天—从 [npst.no](http://npst.no) 检索到的 ets 1232–1—作者

根据 ETSI 232–1 法律，我们拦截了一条加密信息。我们需要弄清楚它说的是什么。它使用与第 8 天 ASN1 相同的加密。

但是输出不一样，都是十六进制的。但是当解密时，我们得到了下面的消息。

对话给了我们一个关于旗帜应该是什么样子的线索。经过一点小技巧后，我发现代码是:c 9c 36 CCF-6a 38–4281-b48f-d 14d b 694d 4 a 3 然后你需要对它进行 MD5 运算，并将其用作标志，这样它就变成了:

> 第 17 天:PST { 0 AE 06 caf 767 AC 7 ebce 290 CFC 57 be 6a 6 f }
> -目前为止一切顺利

# 摘要

不幸的是，这是我狩猎的终点。圣诞节开始到来，其他因素比挪威警方的 CTF 追捕更重要。但对于那些想知道 7 个任务在哪里的人来说，它是 SLEDE8 和基本加密的混合。

我向任何需要与众不同的圣诞日历的人推荐这款 CTF。他们为此付出了很多努力，让每一步都充满乐趣。尽管如此，今年还是有将近 50 %的时间是在 SLEDE8 中进行汇编编程。

这是可以理解的，当他们花了这么多精力来创建开发区时，但是如果能有一点点的混合就更好了。如果你喜欢这个，为什么不看看这个挑战的复活节版呢？

[](/phst-ctf-2021-write-up-e6adf61eb38a) [## PHST CTF 2021 —报道

### 与挪威警察安全局一起捕捉旗帜—复活节版

infosecwriteups.com](/phst-ctf-2021-write-up-e6adf61eb38a) 

或者去看看其他 CTF 的报道？

[](/helsectf-2021-write-up-35d95b7515f7) [## HelseCTF 2021 —书面报告

### 与挪威医疗保健行业的信息安全部门一起捕捉旗帜—复活节版。

infosecwriteups.com](/helsectf-2021-write-up-35d95b7515f7) 

如果你仍然需要一些帮助或指点，总有打嗝套件教程。

[](/basic-burp-suite-usage-c23cf65152f2) [## 基本的打嗝套件用法

### 学习如何拦截和操纵入侵者和中继器的数据。

infosecwriteups.com](/basic-burp-suite-usage-c23cf65152f2) 

我迫不及待地想看看他们明年的 CTF 日历，如果你喜欢阅读？如果你想看更多，请确保给这篇文章 50 个掌声。

![](img/39fc0196e0cee6615c85119c53431dd0.png)

来自 [npst.no](http://npst.no) 的徽标—由 [PST](http://pst.no) 提供