# TryHackMe- Madness CTF 报道(详细)

> 原文：<https://infosecwriteups.com/tryhackme-madness-ctf-writeup-detailed-263fb443b978?source=collection_archive---------0----------------------->

![](img/1f9094e31bddc822fda0f3e7dc772c1b.png)

CTF 报道#12

欢迎各位！！我们将在 [TryHackMe](https://medium.com/u/dc49a0a3cb16?source=post_page-----263fb443b978--------------------------------) 上演疯狂 CTF。

[](https://tryhackme.com/room/madness) [## 疯狂

### TryHackMe 是一个学习和教授网络安全的在线平台，全部通过您的浏览器完成。

tryhackme.com](https://tryhackme.com/room/madness) 

在桌面上为您的 CTF 计算机创建一个目录，并在 CTF 目录中为 Nmap 创建一个目录。

![](img/2d31def56e3f2cf2961a046112edebf0.png)

> 让我们疯狂地潜水吧！！享受流动吧！

![](img/1e6b45617378f3f4b2ddf5bff7e80392.png)

任务列表

## Nmap 扫描:

> nmap-sC-sV-p--关于 nmap/madness<target_ip></target_ip>
> 
> -sC:默认脚本
> -sV:版本检测
> -p-:扫描所有端口
> -oN:输出将存储在您之前创建的目录“nmap”中

![](img/a132ce392a5d4fcb99ed6a57a05f605d.png)

有 2 个端口打开:
22/ssh-OpenSSH 7.2 p2
80/http-Apache/2 . 4 . 18
OS 检测到- Ubuntu Linux

> 导航到 http://<target_ip></target_ip>

![](img/d282b0d10d4ae6e612ca12ab039c92ae.png)

这是 Apache 的默认页面。检查页面的源代码，寻找任何对我们的枚举过程有帮助的有趣信息总是好的。查看 URL 页的源代码。

![](img/6b5c707c939d0d8b0852db7efb407932.png)

有一个评论说他们永远也找不到我。没关系，我们最终会找到他的。在评论上面，我们可以看到有一个“thm.jpg”文件，因此它是提供给我们检查的文件。我们将打开 thm.jpg 来查看它的内容，但它不会，因为图像包含错误。

![](img/b766b1fdc005112639b7fc71bf91b7c1.png)

我们可以使用主机上的“wget”下载图像。

![](img/53fdb0d83ba132d8e585365e6a54f4e6.png)

使用“文件”命令分析文件。

![](img/a0087e0041e99bab477149e6340a9af7.png)

图像文件(如 png、jpg、jpeg 等)损坏或包含错误意味着文件签名不符合扩展名类型，不正确的文件签名不允许我们打开该文件。换句话说，我们必须在 hexeditor 中修改“thm.jpg”的文件签名，并根据图像的扩展类型进行匹配。

我已经链接了下面的文件签名列表:

 [## 文件签名列表

### 许多文件格式不适合作为文本阅读。如果这样的文件被意外地视为文本文件，其…

en.wikipedia.org](https://en.wikipedia.org/wiki/List_of_file_signatures) 

向下滚动，我们可以找到 jpg/jpeg 的文件签名。

![](img/b3689cf622347e28fa00d9b2cd9753a4.png)

我们必须使用“hexeditor”命令修改 jpg 文件的十六进制格式。

![](img/3e4ef3584ecf606840b701b662c4a0cd.png)

将第一行中的十六进制替换为我们之前找到的十六进制后，图像将为我们工作，我们将能够看到图像的内部。保存十六进制[Ctrl+O]并退出编辑器[Ctrl+X]。

![](img/6a278459afdb1f630beb2dfdb7d44e09.png)

> thm.jpg

![](img/04f508959174bec32eeef828c245e177.png)

我们被提供了一个隐藏的目录。

> 导航到 http:// <target_ip>/</target_ip>

![](img/b26df80ad0016d7fa2e127f65be5359a.png)

让我们看看源代码。

![](img/2cc40e1d9401b5f642a6055ef8500ab5.png)

有一个注释，表示页面会认为 0 到 99 之间有一个特定的数字是正确的，在输入正确的键后，我们会得到进入下一步的信息。

Burpsuite 将很容易地解决这个问题，因为我们将能够使用入侵者选项来强力攻击 URL，包括具有从 0 到 100 的数字有效载荷列表的参数。首先，我们必须在浏览器上设置 Burpsuite 代理。就这么办吧。

![](img/da46a04ebb4492e588dcf89f0bbe8be3.png)

> 安装 FoxyProxy 插件。

![](img/2039a6a672826cf3de1fa0d607d9907b.png)

> 点击选项。

![](img/efbc40e9b01cf4767959ae40eb98f5f7.png)

> 点击添加。

![](img/fbd8cfd08568f50021f1682e85271107.png)

> 在 IP=127.0.0.1 和端口=8080 上配置 Burp 代理。

![](img/47e0867170145f1c7884ea8c5c233007.png)

> 打开打嗝代理。

![](img/c748079f8cc1847b5f7e3a52402a94b3.png)

> 在 Burpsuite 中，确保 intercept 处于打开状态，刷新隐藏目录页面[F5 ], burp 将捕获请求。

![](img/be2a20b407ba99ce8f37b8654e5179f7.png)

> 在“位置”标签下的“入侵者”选项中，确保我们想要强力攻击的参数在字符之间。

![](img/6fb004b5b41caf2f080d4889355ece3f.png)

> 在有效载荷选项卡下的入侵者选项中，我们可以从下拉列表中选择数字有效载荷类型，并将有效载荷的选项设置为范围从 0 到 99，1 步意味着逐个使用每个数字。

![](img/2d4c91df48455a9f3c03a63e76c053be.png)

> 开始攻击，等待它结束。

![](img/79cfe11f530681e5fee002935006fdb3.png)

> 单击 Length 以降序排列，我们会看到有一个数字的长度最长。长度越长，获得正确数字的机会就越大，该数字将被页面忽略或验证。

![](img/930755c7bc1b5159028a6b70a43c2f59.png)

数字 73 是我们感兴趣的数字，因此我们将使用它作为 URL 中带参数“secret”的秘密数字。

![](img/0c66b34448bf2b5e2372770d6a0cb08c.png)![](img/44784a3f61e7b1b2405694000bc45832.png)

太好了！！我们已经得到了它的权利。让我们解码这个散列。等等。你可能也认为这是一个杂凑。不，不是的。这是一个密码，我们将使用隐写术工具提取“thm.jpg”文件中的隐藏文件/信息。

我的首选 steg 工具是 steghide。如果你还没有安装它，请在你的机器上安装它。

![](img/534233c6f7da752f0624b72b0de1db08.png)

我们将使用密码从“thm.jpg”中提取信息。

![](img/7b6b3c8cac76e00b6762a73f25d92abe.png)

厉害！！它包含一个用户名和密码被确认。

![](img/b4674e6b168deba0929596cc9aedc90e.png)

用户名很短，但经过旋转，它清楚地向我表明它是 ROT13 编码的。我们想解码它。我破解哈希的首选是赛博咖啡馆。它完美地破解了哈希，工作起来就像一个魔咒。

[](https://gchq.github.io/CyberChef/) [## 网络咖啡馆

### 网络瑞士军刀-一个用于加密、编码、压缩和数据分析的网络应用程序

gchq.github.io](https://gchq.github.io/CyberChef/) ![](img/80436951ad3a3c42bbf7c6796f07294d.png)

解码用户名:XXXXX
我们已经获得了用户名，现在我们需要密码。
如果你还记得[https://tryhackme.com/room/madness](https://tryhackme.com/room/madness)疯狂 CTF 主页上有明确的说明，挑战不需要 SSH 蛮力。

![](img/91f0d8c2147245af5cb3313c81fcb821.png)

但是注意上图。我们都疯了。它被放在那里是有目的的。也许这张图片包含了一些东西，让我们来看看。下载图像。

我们可以使用 steghide 从文件中提取信息。

> 隐藏信息

![](img/21575d5ccb3d48b5b422669cbe1e02b5.png)

> 隐写提取物-sf

![](img/ce699fee1c01aca4cdf13d93027d5341.png)

太棒了！！我们得到了一个密码文件。它将包含密码。

![](img/f1610428c554fa6407146297fdcd99ff.png)

哦，我明白为什么他们指示我们不要暴力使用宋承宪了。单词表肯定无法破解它。然后我们用凭证获取 SSH shell。

让我们嘘。

![](img/32d7ece1f658f555d63754f55f5767ef.png)

我们进去了。让我们捕获用户标志。

![](img/759178dbe64ec5a4c77a7bd88467faa0.png)

提交吧。干得好。接下来呢。是的，你认为它是正确的。我们必须提升权限，成为超级用户。我对机器上的任何潜在攻击媒介进行自动枚举的首选是“Lin peas”——一个真正出色的脚本。

[](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/blob/master/linPEAS/linpeas.sh) [## Carlos polop/特权升级-牛逼-脚本-套件

### PEASS -特权升级真棒脚本套件(带颜色)…

github.com](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/blob/master/linPEAS/linpeas.sh) 

使用 python，我们可以默认在端口 8000 上启动一个简单的 http 服务器，但是你可以指定任何你想要的端口。

![](img/87ceebb7e79af935c845d1669ef222d4.png)

在目标机器上“wget”Lin peas 脚本，使用“chmod +x linpeas.sh”使其可执行，并执行它。

![](img/c6f8ea083dbc3c6a76f92124f28bf415.png)

您还可以通过将命令传送到 tee <output_file>来保存输出</output_file>

![](img/55235b464bafe05b12ea6022f104ee53.png)![](img/65467a743bb03d36f8e99cca319cceac.png)

此脚本最精彩的部分是，用红色和黄色突出显示的内容有 99%的机会成为攻击媒介，从而提升权限。攻击媒介是 root 拥有的二进制文件，其“s”权限意味着超级用户。
我寻找可利用的二进制文件的途径

[](https://gtfobins.github.io/) [## GTFOBins

### GTFOBins 是 Unix 二进制文件的精选列表，攻击者可以利用它来绕过本地安全限制…

gtfobins.github.io](https://gtfobins.github.io/) ![](img/21894e25c578923824fe7f2ec9d5703f.png)

这些命令在 shell 上无法工作，也无法提升权限，因为我们有 SSH Shell，但是我们没有用户的密码，因此任何使用 sudo 的命令都无法执行。

我们可以寻找任何利用'屏幕-4.5.0 '的漏洞，是的，我们有一个。下面的链接为利用。我会把漏洞弄到我的机器上。

[](https://www.exploit-db.com/exploits/41154) [## 攻击性安全利用数据库档案

### GNU 屏幕 4.5.0 -本地权限提升..Linux 平台的本地漏洞利用

www.exploit-db.com](https://www.exploit-db.com/exploits/41154) ![](img/ef697dd407584eb9b4a292a6c2f7b8e8.png)

我们会分析剧本。

![](img/417ae21ffc6619094aed2da0e26f3dee.png)

我们需要在/tmp 目录中执行脚本，以便它正常工作并提升权限。

![](img/21f00255803ee3a74a1a1202f67de3e8.png)

抄剧本。

![](img/092ca2c50e06f286074c921f209ef896.png)

使用 nano 并创建“exploit.sh”文件。

![](img/5e2458f09c54a6bb8e0005fe0e467d05.png)

粘贴

![](img/826f8d000c80870dab0d07d8f8f00471.png)

保存它[Ctrl+O]并退出[Ctrl+X]

使其可执行。

![](img/35796012b6dec32131e8d0621dceb275.png)

观看魔术表演。

![](img/0aa5fdc7456e4117b2a3eccbec1c81e8.png)

确认我们是根。

![](img/fce7e4efe5fc9d5d5fd683ea8bd62d7e.png)

的确如此。宏伟。

让我们夺取根旗，给这场疯狂一个圆满的结局。

![](img/250b9d23bcb13421e2f8fb28e718db8e.png)

瞧。
提交。我们已经成功地将箱子扎根。我从中获得了很多乐趣。我希望你能从阅读中获得乐趣。

![](img/13c9d11b75349f5efcf06a37c3e3817a.png)

#]

如果你喜欢这篇文章，并且这篇文章对你有所帮助，请在评论中告诉我，或者用掌声分享你的爱。

谢谢你抽出时间。

跟着我。

更多的报道正在进行中。

保重，注意安全，继续黑！

哈桑·谢赫