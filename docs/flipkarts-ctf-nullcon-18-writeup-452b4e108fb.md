# flipkart 的 CTF @ nullcon ' 18[特写]

> 原文：<https://infosecwriteups.com/flipkarts-ctf-nullcon-18-writeup-452b4e108fb?source=collection_archive---------0----------------------->

**Web 1:(旅行者)**

![](img/5cb2545eea4cad9686617cb5215d7ee2.png)

这是第一次网络挑战赛的任务。*【从这里开始旅程】*的链接登陆如下页面。

![](img/a6f4c4ee6cea0b4244984494feacaadc.png)

在检查点击下载按钮后触发的请求时，显然这是一个路径遍历挑战。

![](img/51461c5ebd925d4302a286479d43154d.png)

向下滚动到页面底部时，标记位置被添加了注释。

![](img/10740151ab007431266691a5d2fd8836.png)

所以想法是使用路径遍历攻击到达 *ctf* 目录下的*标志文件*。但是扭曲正是我们需要操纵参数的地方。

最初试图操作 filename 参数，

![](img/85555080d79da2bd9687e2fc1d92e87c.png)

但如果参数包含 **"/ctf"** 或"**.. "则返回 beep**。试着编码这些东西。但是失败了。稍后检查请求上的 **x 用户** cookie。更改 cookie 时返回完整性检查失败

![](img/612bf0983c1064774371ec4ec0f37dd0.png)

原来如此！以下是 xuser cookie 的结构:

x-用户: <filename>-</filename>

![](img/fbf194e6c4ec99f036ec683b2c4f68be.png)

所以尝试用**遍历文件路径../../../../../../../../../ctf/flag** 。但是他们去掉了“..”后面的“/”

![](img/369cea56b3bc0eae954a5fffc2eade52.png)

所以在同一个有效载荷上尝试了“/”的双重编码。

![](img/5ecf75a4477dedf16753983d1ca0ca37.png)

哦耶！我们拿到旗子了😎

**Web 3:(抢旗)**

![](img/36e581e7be3859d42d941ea093b99fb2.png)

所以这有点像 SSRF 的弱点。该链接位于以下页面

![](img/11c5d6321d78c5b77c8c32b12428efba.png)

任务是在同一个 web 根目录下读取“flag.php”。但是当从浏览器直接访问时会被阻止(这就是任务)

![](img/0137e31928f6ef503f27babcf3a957d0.png)

所以在获取 URL 上尝试了[http://127 . 0 . 0 . 1/flag . PHP](http://127.0.0.1/flag.php)，它返回了以下内容。

![](img/8b915304aaa4b5df5f666090fb686309.png)

所以干脆用 0.0.0.0 代替 127.0.0.1 地狱耶！成功了😎我们拿到旗子了。

![](img/14072918fecf14523afc8f4c00bbdd15.png)

> 你真的认为会那么容易吗？

不，但它是😂 😂

感谢挑战 flipkart 安全团队！

-H4 ckx 0 r 5 团队