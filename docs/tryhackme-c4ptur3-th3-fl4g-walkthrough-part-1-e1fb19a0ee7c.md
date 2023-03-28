# TryHackMe(c4ptur3-th3-fl4g)演练第 1 部分

> 原文：<https://infosecwriteups.com/tryhackme-c4ptur3-th3-fl4g-walkthrough-part-1-e1fb19a0ee7c?source=collection_archive---------0----------------------->

![](img/aaa9fecf6ae90d08ecbe8bff962e32b3.png)

朋友们好，这是我的第二篇文章。在这篇文章中，我将讨论我是如何解决加密难题的。在大多数的夺旗比赛中，都会有加密类别。所以这个博客将帮助你解决初级加密的挑战。

挑战链接:

[](https://tryhackme.com/room/c4ptur3th3fl4g) [## TryHackMe | c4ptur3-th3-fl4g

### TryHackMe 是一个学习和教授网络安全的在线平台，全部通过您的浏览器完成。

tryhackme.com](https://tryhackme.com/room/c4ptur3th3fl4g) 

**【任务 1】平移&换挡**

1.  **c4n you c4p 7u 23 7h 3 f149？**

**回答**:你能夺旗吗？

这是一个惊人的挑战。我们很多人都听说过黑客的花哨用户名。所以这种基本的密码叫做**李特码编码。**

[](https://www.dcode.fr/leet-speak-1337) [## Leet Speak 翻译器— 1337 5p34k —解码器、编码器、解算器

### 在 leet (1337)中翻译/编写的工具。Leet speak 1337 5p34k，使用字符和符号以某种…

www.dcode.fr](https://www.dcode.fr/leet-speak-1337) 

2.**01101100 01100101 01110100 011100011 0011100000 011110010 011110001 0011100011 01101101101111 01101101 01110001 01110001 01110001 0110001 01100000100000000101010101010010101010101000101000**

**回答**:让我们尝试一些二进制出来！

它看起来像二进制值。所以我们必须把它解码成可读的文本格式。

![](img/6dd66ca111e03695d71aa8792abc41f6.png)

二进制到文本

[](https://gchq.github.io/CyberChef/#recipe=From_Binary%28%27Space%27%29) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=From_Binary%28%27Space%27%29) 

3.**mjqxgzjtgiqs 4 zaon 2 xazlsebrw 63 lnn 5 xca 2 loebbvirrhom = = = = = =**

**回答** : base32 在 CTF 超级普遍

该文本看起来像 base 32 文本。当我玩许多 CTF 时，我看到 base32 编码的文本更多地等于(=)符号。所以我解码到基数 32，我是正确的。

![](img/9c4eae799d5ec18903e4ae231004d2d8.png)

base32 解码

[](https://gchq.github.io/CyberChef/#recipe=From_Base32%28%27A-Z2-7%3D%27,true%29) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=From_Base32%28%27A-Z2-7%3D%27,true%29) 

4.
**rwfjacbcyxnlnjqgzglnaxqgcmvwcmvzzw 50 cyblegfjdgx 5 idygyml 0 cybvzibkyxrhlg = =**

**答**:每个 Base64 位正好代表 6 位数据。

根据前面的问题，我们看到基本编码有一个等号。据我所知，base64 编码在编码字符串的末尾有==符号。

![](img/e7f515b9ac45d6d9ea491623e422075f.png)

base64 解码

[](https://gchq.github.io/CyberChef/#recipe=From_Base64%28%27A-Za-z0-9%2B/%3D%27,true%29) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=From_Base64%28%27A-Za-z0-9%2B/%3D%27,true%29) 

5.**68 65 78 61 64 65 63 69 6d 61 6c 20 6f 72 20 62 61 73 65 31 3f**

**回答**:十六进制还是十六进制？

我们已经知道这是十六进制的格式，所以我们必须解码成文本格式来得到这个标志。

![](img/6aa7d45ad501185e9ff8546df68016d3.png)

十六进制解码

[](https://gchq.github.io/CyberChef/#recipe=From_Hex_Content%28%29&input=RWJnbmdyIHpyIDEzIGN5bnByZiE) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=From_Hex_Content%28%29&input=RWJnbmdyIHpyIDEzIGN5bnByZiE) 

6.Ebgngr zr 13 cynprf！

**回答**:把我旋转 13 个位置！

正如我在问题中看到的，一些提示已经存在。13 是解决这个挑战的直接提示。在 crypto 中，有一种 ROT 编码来编码文本。

![](img/cbdf58d578e3e06b394e2f8ccff567c1.png)

ROT13 解码

[](https://gchq.github.io/CyberChef/#recipe=ROT13%28true,true,13%29&input=RWJnbmdyIHpyIDEzIGN5bnByZiE) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=ROT13%28true,true,13%29&input=RWJnbmdyIHpyIDEzIGN5bnByZiE) 

7.
*@F DA:？> 6 C:89E C@F？5 323J C:89E C@F？5 Wcf E: > 6DX

**回答**:你让我向右旋转，宝贝向右旋转(47 次)

这个密码对我来说完全是新的，因为我在以前玩过的 CTF 中从未见过这种编码。所以我很快在谷歌上搜索了一些例子，我知道这是 **ROT47 密码。**

![](img/eba46ab4a6ad9599d3096ebeb601a7b8.png)

ROT47 解码

[](https://gchq.github.io/CyberChef/#recipe=ROT47%2847%29&input=RWJnbmdyIHpyIDEzIGN5bnByZiE) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=ROT47%2847%29&input=RWJnbmdyIHpyIDEzIGN5bnByZiE) 

8.**——。。-..。-.-.— — — — ..- -...-.-.。- — ..— — -.**

**。-.-.-.— — -....-.— .**

**回答**:电信编码

这是每个 ctf 玩家都知道的编码技术，因为在大多数初级 ctf 中，你会遇到这种类型的解码挑战。所以这是**莫尔斯电码密码，**

![](img/3b0b23c117c7bbb6a8a720dfbe30d4d5.png)

莫尔斯电码解码

[](https://gchq.github.io/CyberChef/#recipe=From_Morse_Code%28%27Space%27,%27Line%20feed%27%29&input=LSAuIC4tLi4gLiAtLi0uIC0tLSAtLSAtLSAuLi0gLS4gLi4gLS4tLiAuLSAtIC4uIC0tLSAtLgoKLiAtLiAtLi0uIC0tLSAtLi4gLi4gLS4gLS0u) [## 网络咖啡馆

### 网络瑞士军刀——一个用于加密、编码、压缩和数据分析的网络应用

gchq.github.io](https://gchq.github.io/CyberChef/#recipe=From_Morse_Code%28%27Space%27,%27Line%20feed%27%29&input=LSAuIC4tLi4gLiAtLi0uIC0tLSAtLSAtLSAuLi0gLS4gLi4gLS4tLiAuLSAtIC4uIC0tLSAtLgoKLiAtLiAtLi0uIC0tLSAtLi4gLi4gLS4gLS0u) 

9.**85 110 112 97 99 107 32 116 104 105 115 32 66 67 68**

**回答**:拆开这个 BCD

所以乍一看，这些数字看起来很正常，

对吗？

但是我们错了，因为每个计算机/技术人员都知道那是十进制数字。所以，很容易识别这个数字。原来是**十进制数解码。**

![](img/f45a68423924cc290e27acadd8f534a3.png)

十进制到文本解码

10\. **LS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0=**

**回答**:让我们把这个弄得更复杂一点…

那么让我们开始为任务 1 的最后一个挑战而战吧。

我第一次感到困惑，所以我第一次看到最后一个挑战。但是当我向下滚动时，我看到了字符串末尾的=。所以认为这可能是基本编码。

现在链条将

Base64 ->莫尔斯电码->二进制->ROT47 ->十进制

这种挑战需要逻辑思维。from = to 标志。

这个多重编码挑战者结束了。

我希望你们喜欢我在 tryhackme.com 的 ctf 挑战赛中的演示。

**我将在下一篇文章中发表第二部分。**

作者:**马尤尔·帕尔马(th3cyb3rc0p)**

[](https://www.linkedin.com/in/th3cyb3rc0p/) [## Mayur Parmar -创始人& CTF 玩家-网络叛逃者| LinkedIn

### 💻我不跟踪，我 Investigate🕵️Experienced 安全研究员与一个证明的历史工作在…

www.linkedin.com](https://www.linkedin.com/in/th3cyb3rc0p/) 

[https://twitter.com/th3cyb3rc0p](https://twitter.com/th3cyb3rc0p)

 [## 脸谱网

### 登录脸书，开始与你的朋友、家人和你认识的人分享和联系。

www.facebook.com](https://www.facebook.com/th3cyb3rc0p)  [## 登录* Instagram

### 欢迎回到 Instagram。登录查看您的朋友、家人和兴趣爱好捕捉和分享了什么…

www.instagram.com](https://www.instagram.com/th3cyb3rc0p/) [](https://www.linkedin.com/company/cyberdefecers) [## 网络叛逃者| LinkedIn

### 网络叛逃者| LinkedIn 上的 83 名粉丝|我们是一群充满激情的信息安全研究人员和 CTF…

www.linkedin.com](https://www.linkedin.com/company/cyberdefecers)