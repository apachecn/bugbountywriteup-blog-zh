# SANS Holiday Hack(kring lecon)2020 报道—第 2 部分

> 原文：<https://infosecwriteups.com/sans-holiday-hack-kringlecon-2020-write-up-part-2-617e1870ed2e?source=collection_archive---------2----------------------->

> 这是我的 SANS Holiday Hack 文章的第 2 部分，涵盖了目标 5-8 和在以下房间进行的相关小型试验:KringleCon 会谈、演讲者未准备好的房间、车间和屋顶。

第一部分

![](img/8d9097ce12fea842ac958d12a4282f9e.png)

# 克林贡会谈

这一层包含 3 个性质相似的侧面目标。

## 扬声器 UNPrep

进入演讲人准备好的房间的门在一个侧面目标的后面，在一个二进制文件中找到一个密码。还有两个相似的目标，打开房间内的灯和自动售货机。对于所有 3 个目标，你都可以进入`~/lab`文件夹，修改二进制文件的副本。

**门——容易**

1.  在 door 二进制文件上运行 strings 命令，并输入找到的密码。

![](img/73b1a3d4c989c94fa799a1c8aec4db48.png)![](img/a3b89309a7f68850b94be3647bf210e6.png)

**灯—中等**

1.  Lights 有一个包含加密密码的. conf 文件

![](img/a2c3524abdae725a97228526ace1c653.png)

2.跑步。/lights 显示配置的用户名被打印出来。也许值得检查一下用户名是否会自动解密密码

![](img/595655df83419d1e22857f4b5559f2d6.png)![](img/eeaa212ba441a26ed425ce9f8a14447c.png)![](img/c9ee83211fbd3332ca634d51c2e726b2.png)

3.输入解密的密码

![](img/2d7c763b78aa2ccd9bac65fd0b4eead8.png)

**自动售货机—硬**

1.  同样的技巧在这里不起作用，但可以删除配置文件和。/vending-machine 允许您输入新用户/通行证并输出加密值。

![](img/8d1f701062af8e49afb432c6ec45f5fa.png)

2.既然我们知道了明文和密文，只需要想出加密的方法就行了。

3.我喜欢从添加大量的一个字母开始，看看。

![](img/a3a050a6b2a1bf4b022cc10f24acb18a.png)![](img/11b80b7579412d57380af7f169a8c746.png)

4.然后我试着混合已知的字母。

![](img/56e7aaec4c17170f130ea38cebcecc09.png)

前半部分匹配 A 8 字符模式的前半部分，后半部分匹配 B 8 字符模式的结尾。密码中的每个字母位置将与明文模式的密码模式中的相应字母位置相匹配。

*如果密码= p，则 p[x] =密码模式[x]*

《出埃及记》如果明文密码是 ABAB，那么密码就是 XqGT 或 A[1]B[2]A[3]B[4]

5.Python 来拯救找出其余的密码模式，并解决这个问题。

代码的第一部分创建了一长串要编码的字母，然后作为 encoded_data 变量加载。然后对该变量进行解析，以创建每个字母到其 8 个字符密码的映射。

![](img/2c1b82130a8cfc1e1c980fa24ffb5138.png)

代码的结尾将给定的密码拆分成一个八位字节，并通过已知值对来解密 CandyCane1 的答案。

![](img/fab370aa03e03862d3238750ce8451f1.png)

# 扬声器准备室

这里有一个附带目标，与 PRNG 一代有关。

# 打雪仗

和游戏机旁边的 Tangle Coalbox 对话给出了一些重要的提示，包括[的链接 https://github . com/kmyk/mersenne-twister-predictor/blob/master/readme . MD](https://github.com/kmyk/mersenne-twister-predictor/blob/master/readme.md)，会大量使用。

1.这个游戏的工作方式如下，每次你产卵时，你和敌人的棋盘都是由一个伪随机数发生器决定的。由于你可以打开游戏的多个实例，当试图解决困难的水平，你只是给了一个固定的数字，你可以把这个数字加载到简单的水平，击败它，然后重放困难的。对于 impossible，没有提供数字，但是在 HTML 注释中有一个 624 个以前尝试过的“随机”值的列表。使用这些值，您可以预测您的下一个值应该是什么，并执行加载到简单游戏中的相同过程。

2.加载是不可能的，点击 F12 并查看 html 源代码，会找到值列表。

![](img/0877c2a832e0bd8b96e88497d62de3ff.png)

3.复制并清理到只有数字

![](img/53b9563e068f30e4e3486e7658460cd1.png)

4.安装之前提到的 Mersenne twister，并将给定的值输入预测器，输出第一个结果。

![](img/efaf6eafcc4bfe04e58c40f5add5edfa.png)

5.将该值复制到简易板中，如果操作正确，您的两块板应该具有完全相同的起始位置，表明敌人也是如此。然后你可以在 easy 上算出答案，在 impossible 上重演。

# 电梯(绿色+红色)

提供通往车间和屋顶的通道。再次，多种方法来解决，这是我使用自动售货机的门户网站。

![](img/5cebba33d31c2c6ab347d02ffc17b52f.png)

# 研讨会——目标 5

在工作室里，有一个次要目标，以及目标 5，它需要在隔壁房间找到一个物品。

## 自动排序正则表达式

直截了当地解决每个正则表达式挑战。前 3 个很简单，接下来的几个充分利用了边界组，将每个问题分成小组。

![](img/35e710148b50704ef1f800f79fc9c641.png)

# 目标 5 —打开 HID 锁

为此，我们需要打开车间的锁，但要这样做，我们首先需要获得包装室的 Proxmark。一旦获得，就采取以下步骤。

1.  记得在厨房里，Shinny Upatree 被认为是值得信任的。Shinny 位于前面的草坪上，所以请让开，站在 Shinny 附近
2.  在项目中打开 Proxmark CLI

![](img/09cabad2429b2b5a9bc1435a3234ebff.png)

3.在接近 Shinny 时运行`lf hid read`并保存它给出的值

![](img/b8232e07761a0281ce98d351fbbd877a.png)

4.回到车间袖手旁观门并运行`lf hid sim -r <id value>`

![](img/31a89fabf7435808972f55ce43252f75.png)

5.车间的门现在应该是开着的

> 重要提示—穿过下一个房间会让你变成圣诞老人，这样你就可以进入特定的区域和终端。将来我扮成圣诞老人去的任何地方都会被标记出来。

# 大会堂—目标 6

# (圣诞老人)目标 6 — Splunk 挑战

目标:访问大房间中的 Splunk 终端。圣诞老人害怕攻击克林果的敌对组织的名字是什么？

这是一个 Splunk 挑战赛，提供 7 个问题，必须在 Splunk 索引中搜索并找到正确答案。

![](img/79d2f1141a10dfc92978c9ca28283122.png)

# 屋顶——目标 7

屋顶(也称为网络战争)两个侧面目标和两个主要目标。这两个次要目标可以在任何时候完成，但主要目标只有在你成为圣诞老人后才能完成。

## Scapy Prepper

这个附带目标旨在通过询问一系列问题的解决方案来教授 python 模块 scapy 如何工作的基础知识。表中给出了解决方案

![](img/0c266a0394b3b5c45d635b1cd5b07ebb.png)

## Can 总线调查

目的是找到 CAN 总线转储中的锁定-解锁-锁定序列，并输入解锁的时间值。

1.  登录终端— cat candump

![](img/ff7588902c34ef3b31fac63d833efded.png)

2.用 grep inverse -v 标志删除多余的 188 和 244 值。

`cat candump.log | grep -v “244” | grep -v "188"`

![](img/87d134b2c2cc6cf657ca61cfdca8eb33.png)

3.中间值是正确的解锁值。

![](img/775296540654b11c55959f5e34ceac7e.png)

# (圣诞老人)目标 7 —圣诞老人 Can-D 巴士

目标:白色杀机不知何故在雪橇的 CAN-D 总线上插入了恶意信息。我们需要你排除恶意信息，而不是其他人来修理雪橇。参观屋顶上的 NetWars 房间，并与 Wunorse Openslae 交谈以获得提示。

1.  点击雪橇，通过排除 080，019，188，244 删除现有的垃圾邮件。

![](img/4f4056dd85959cea166b686256a4a15c.png)

2.测试制动器，给出两个值，正确值和一个额外的 FFFFFF 值。

![](img/3151fa5ce5e15c5aa534de3343d7df56.png)

3.排除 FFFFF 值

![](img/e1f0f17ab7d699663614acc07e874ce4.png)

4.测试门

Lock = 19B#00000000000

解锁= 19B#00000F000000

注意正在进行的 19B#0000000F2057，排除

![](img/a212010b5ca0e3b89739f078f8270f20.png)

5.删除早期的调试值

![](img/08ade1d45a0908c09d49649f770e66d6.png)

# 包装室—目标 8

# (圣诞老人)目标 8 —损坏的标签生成器

目标:帮助 Noel Boetie 修理包装间的[标签生成器](https://tag-generator.kringlecastle.com/)。环境变量 GREETZ 中的值是什么？请与厨房的 Holly Evergreen 联系，寻求帮助。

标签生成器位于 https://tag-generator.kringlecastle.com/的[。解决方案如下:](https://tag-generator.kringlecastle.com/)

1.  上传随机 txt 文件并注意错误路径

![](img/ea6ddf8ab1543a8ae9dd7f4604aa99e5.png)

2.打开 Burp 或其他代理，上传一个小的普通 jpeg 图像，注意图像的提取路径

![](img/dbb2276a8d84c0103c6ed5ebc05c063f.png)

3.用`?id=../app/lib/app.rb`在/image 页面测试 LFI

![](img/60533cd08e83c94caba6e3c87b978b15.png)

4.因为我们可以读取任何文件，问题是找到一个环境变量，所以我们可以读取 linux 中/proc/self/environ 下的所有 env。

> 我假设这里有另一个使用 zip 文件的方法，因为源代码指出了这一点。

![](img/5f4c60a3bf5ec2bfda8863c0b662c586.png)

5.答案:JackFrostWasHere

[**第三部分**](https://medium.com/bugbountywriteup/sans-holiday-hack-kringlecon-2020-write-up-part-3-5cdb627363ae)