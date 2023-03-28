# 初学 picoMini CTF 2022 —详细报道

> 原文：<https://infosecwriteups.com/beginner-picomini-ctf-2022-writeup-94174d0ea64b?source=collection_archive---------0----------------------->

![](img/c695071ac13f35b60bb1032e33ebe867.png)

在这篇文章中，你可以找到所有初级 picoMini 2022 挑战的文章。

1.  runme.py

![](img/2eccc4c9b0c34e804bed9441a4123cf7.png)

下载给定的 python 脚本并使用 python3 运行它。我在 wsl 中使用 python3

![](img/f023f7efdf0f3025b2a60445941fb617.png)

runme.py 的标志

2.ncme

![](img/558e23fdc3a557cf2373f91abfa27db6.png)

这是直截了当的，你可以复制给定的命令并粘贴到终端来获取标志。

![](img/3f7ea22430b2d705be9237d93794b1a9.png)

ncme 的标志

3.convertme.py

![](img/7f0bb4e9db0018cb03314511ce41cf80.png)

下载给定的 python 脚本并运行它。

![](img/635b2b2c4e18e3b2d3db852a7a07167d.png)

现在将给定的十进制值转换为二进制，您将获得标志。

![](img/8474115ac1b9a1d991c9ec300f350e80.png)

convertme.py 的标志

4.电报密码本

![](img/0a2278654f59156e2e6ffe4651575047.png)

下载相同目录中的给定文件。

打开 python 代码并进行分析。

![](img/a63b3c9967d822d02cde91b7c34e5c69.png)

main 函数只打印 codebook.txt 文件中的标志

所以运行 python 脚本。

![](img/f8567806f16bf7931ec458f0ffed3ca2.png)

码本标志

5.fixme1.py

![](img/4f21fdf3ca08fa77f882a6bac04e767e.png)

下载脚本。

打开来分析。

![](img/7c8a5423026f4518ab84c1ce954e4c6f.png)

运行时，我得到了这个错误。

似乎我们对给定的 python 代码进行了错误检查。当我打开 vs code 中的代码时。我能在最后一行看到导致错误的缩进。我们已经编辑了代码并运行了它。

![](img/0a62a7c1f6b770bbef01a44fc76cd7a9.png)![](img/01662c5c0a889833cc32640cfab667e2.png)

fixme1.py 的标志

6.fixme2.py

![](img/184b3e692fe8c43ddbd156d6c6f496c7.png)

下载脚本。

![](img/bfc39109bb0f3a1c6848a9f8fa461e3f.png)

我们已经清除了上述错误。条件运算符应该是“==”，而不是赋值运算符“=”。

所以代码看起来像这样。

![](img/490b099f674d599be68be8c08256ae2a.png)

现在让我们运行代码。

![](img/32c2b960832b484c44590d04d989e216.png)

fixme2.py 的标志

7.PW 裂纹 1

![](img/fd1228df803977cb296e639c1ffe56bb.png)

下载相同目录下的文件。分析 python 脚本，我们可以看到密码是 17ac。

![](img/dcf8f58d849a8eae834e7447d1e2acb9.png)

我们获取密码并运行 python 脚本。

![](img/dc48cf0977fa57a566f706a5887d09b8.png)

PW 裂纹 1 的标志

8.故障猫

![](img/79623d8ca409d06d12d2a2f2102a7d8a.png)

使用 netcat 连接到服务器。

![](img/e15642dad258a0e4fdc85ef05a3a4040.png)

标志不完整，其中有一些 ASCII 字符。我们必须转换它。使用 python3 打印标志字符串，这样就可以得到正确的标志。

![](img/8aa03a9ba22b9a496c06898306de8b56.png)

故障猫标志

9.PW 裂纹 2

![](img/ef9f6373618d34bc75b18453c06817e5.png)

下载完文件后，分析一下。

![](img/0e83f127f5985040c7dc7cd31363cb7c.png)

他们给出了密码中每个字母的 ASCII 值，我们已经转换了它们。使用 python3 终端工具，我打印了 ASCII 字符。密码是 5909。

![](img/5cc12f446715d33aca082364033283fb.png)![](img/2c4e0e49bfda3be730b37134b7afaf92.png)

PW 裂纹 2 的标志

10.HashingJobApp

![](img/6cdda8826cf1db09bca56df2e6c1723e.png)

使用 netcat 连接到服务器，您将在 md5 中得到一个要散列的字符串

![](img/bb70ea57e1c6a1b081b251f25d434dbe.png)

我使用了 [md5](https://www.md5hashgenerator.com/) 哈希生成器来哈希字符串。也可以使用“md5sum”命令行工具。

你将得到三个不同的字符串散列，你将得到你的标志。

![](img/2de1a11acc507181157e0496f5f7cedf.png)

HashingJobApp 的标志

11.蛇纹石

![](img/3af3faeba95a5de39ab8af2ca975c808.png)

打开 python 脚本。它包含三个不同的功能打印鼓励，打印标志，并退出。

我们需要打印旗帜，我选择打印旗帜。但是它回来了

![](img/c0412675ad66722d6cdbb8871466cd72.png)

当我检查 python 脚本时，print_flag 没有被调用

![](img/a9caed8b00d5d47a7534524e686f6ca3.png)

我们所要做的就是编辑代码并在选项 b 中添加打印标志函数。

现在看起来是这样的。

![](img/f56e9a0dacc618520a7cf8c6c971ffa7.png)

运行脚本并获取标志。

![](img/697263a877cdb3085382dff82c8d7637.png)

蛇形标志

12.PW 裂纹 3

![](img/8f560965b0f638155ada0d4ee17ae10a.png)

下载相同目录中的文件。打开 python 脚本并进行分析。

![](img/f2e94afd66618f2b2cd33e5e39844e34.png)

有一个可能的密码列表，我们只需检查它们。

我一个接一个地输入以找到正确的密码，因为只有 7 个。

密码是 f634。

![](img/83297c3947d359b61031ac9d38fc7337.png)

PW 裂纹 3 的标志

13.PW 裂纹 4

![](img/e88292f3ea3183d96faa8a56bc4d06ad.png)

这个挑战和上面的一样，但是有很多可能的密码。

按如下方式更改 python 脚本。

![](img/f700d9df9ad74bdc3f87dd41e9552cd3.png)

现在运行代码

![](img/c3224c8271e55e9750996027f4135643.png)

PW 裂纹 4 的标志

14.PW 裂纹 5

![](img/8432f0889c5df0fbc04dcaf85c51318d.png)

下载相同目录中的文件。这次我们有一个包含所有可能密码的字典文件，我们必须找到正确的密码。

按如下方式更改 python 脚本。

![](img/b0a88be8e0fcf65199008d28c58c8d33.png)

现在运行代码。

![](img/37d02fdc2d7364c3716b28dbf4bf8dff.png)

PW 裂纹 5 的标志

这个 CTF 有非常初级的挑战。无论谁是 CTF 或网络安全的新手，都可以以此为起点。

阅读我的其他文章。

网址:[https://vishnuram1999.github.io/](https://vishnuram1999.github.io/)

更多其他文章请关注我。

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。[查看更多详情并在此注册。](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)