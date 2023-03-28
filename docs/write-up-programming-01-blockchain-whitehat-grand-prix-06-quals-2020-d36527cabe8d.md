# [撰写]编程 01 &区块链—怀特哈特大奖赛 06 — Quals 2020

> 原文：<https://infosecwriteups.com/write-up-programming-01-blockchain-whitehat-grand-prix-06-quals-2020-d36527cabe8d?source=collection_archive---------0----------------------->

# 编程 01

**问题**

```
nc 15.164.75.32 1999
```

**回答**

```
PROGRAMING - WHITEHAT GRANDPRIX 06:

--> COUNT THE NUMBER OF POSSIBLE TRIANGLES <--

HOW MANY TRIANGLES ARE CREATED BY N (1..N) NUMBER. N < 10^6

Example:  N = 5
OUTPUT : 3 

(2,3,4),(3,4,5),(2,4,5)
................/\...................|\...................
.............../  \..................| \..................
............../    \.................|  \.................
............./      \................|   \................
............/        \...............|    \...............
.........../          \..............|     \..............
........../____________\.............|______\.............

n = 11
Answer: %
```

所以问题要求求边长为整数的三角形的个数，可以用 1 到 N 的自然数创建，因为 N 很大(> 99999)，所以极有可能会有答案的数学公式，我们可以 google 或者 sit，分析算法。但是我很无知，所以我会谷歌:((。快速代码 1 程序为问题的目的寻找直角三角形:

然后尝试用小 N (4，5，6，7，…)运行，我们得到如下序列:

```
➜  whqual2020 python brute_triangle.py
1
3
7
13
22
34
```

谷歌一下，找到 https://oeis.org/A173196

> a(n-1)是边长最大的整数边不等边三角形的个数<= n, including degenerate (i.e., collinear) triangles. a(n-2) is the number of non-degenerate integer-sided scalene triangles. — Alexander Evnin, Oct 12 2010

OK, so the remaining job is to implement the calculation formula, connect and get the flag (note the offset of N in question against n of the sequence):

Run and get the flag

So the flag is 【

# Blockchain — Misc

**问题**

```
Blockchain application in IOT system.
Using vulnerable chipset to generate public keys.

http://52.78.210.118/Blockchain.zip
```

**回答**

乍一看标题，我以为会有一些与区块链有关的东西，包括哈希、时间戳、块等等，但一旦完成，它只是一个伪装的加密帖子😧。解压缩我们的文件:

```
.
├── 34a7370734caff5d129ad355f78f3ccf.pem
├── 8a95963d7bedd2b81ad09cd1838c7a4d.pem
├── block1.json
├── block2.json
├── block3.json
└── flag.zip
```

这个`flag.zip`文件里面有一个带密码的`flag.txt`文件，我们的任务就是找到密码来解码这个文件。回顾 2 pem 文件，公钥很短，给文章`Using vulnerable chipset to generate public keys`加个提示。因子可能是，或者这两个公钥具有相同的因子。而且确实问题在第二个方向。我们很快找到了两个键对应的 p 和 q:

```
# 8a95963d7bedd2b81ad09cd1838c7a4d

p1 = 1091951834898382993408357240646061116416467734213916798265279491274843400183
q1 = 968357930958770928862265655524254201820039464684491130864944605493368598601

# 34a7370734caff5d129ad355f78f3ccf
p2 = 1091951834898382993408357240646061116416467734213916798265279491274843400183
q2 = 3602083547017910155331521957638413821351348404017103506647493207187611603783
```

看看这个`block1.json`

```
{
  "data_block": [
    {
      "34a7370734caff5d129ad355f78f3ccf": {
        "messger": "864826346328927043007924641380681736981324987926997370887020532699182309378599192043216478265476219278213123962074508284028662403643532629433329761492"
      }
    },
    {
      "8a95963d7bedd2b81ad09cd1838c7a4d": {
        "messger": "259242051785557714557594066190019826465030870294179284671916925100489488841761299528416294893049464518482888070747927907550583942630013791833474340284"
      }
    }
  ]
}
```

我们尝试用相应的私钥对 2 条消息进行解密，会产生一个明文，令人惊讶的是，块 2 和块 3 都可以做到这一点(不考虑前面的块？！！似乎是由于检查被移除，仅留下内部的数据)。快速代码解码文件:

并运行代码:

```
➜  whqual2020 python blockchain.py 
Password using open flag.zip
Do you understand the blockchain?
Password = Password1+Password2
flag in flag.txt
Password2:'D@V!4P##Ij'
Password1:'irVOwoJR7d'
```

使用密码`irVOwoJR7dD@V!4P##Ij`解压文件`flag.zip`我们得到一个新文件，内容是 base64，*解码*得到一个二维码图像。扫描二维码，然后我们捕捉旗帜:

![](img/57caee5239b94566351a306988e6a6e1.png)

标志:`Whitehat{the_ flag_blockchain_ iot}`

好好享受吧伙计们。

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)