# H4CK3Rs 和 CTF 播放器 0x1(编码)的基本加密技术。

> 原文：<https://infosecwriteups.com/cryptography-essential-for-h4ck3r-and-ctf-player-0x1-encoding-b638ab5821a9?source=collection_archive---------1----------------------->

![](img/d2565e9dcbf76d23d10b624f0c0ce7c1.png)

由 [sippakorn yamkasikorn](https://unsplash.com/@sippakorn?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

你好，亲爱的黑客们，在这个系列中，我们将教你密码学的基本概念，这在 CTF 竞赛和黑客挑战赛以及网络安全考试和面试中非常有用。

## 什么是密码学？

密码术是在第三方存在的情况下对用于安全通信的技术的实践和研究，简单地说，它是将数据转换成不可读格式以保护数据不被未授权的人获取的过程。

## 为什么它很重要？

正因为数据是任何人的一切，所以如果我们想保护它免受恶意用户或非故意的人。那么保护我们的数据是必须的，同时也要保持中情局交易的机密性。

## 基本术语:

*   **密码**:执行加密或解密的算法。
*   **明文**:未加密或“原始”消息。
*   **密文**:加密的消息。
*   **Key** :一条信息，规定了明文到密文的转换，解密算法反之亦然。

![](img/ea2ef37ef48c7011122f3b025c057295.png)

[杰克·约翰逊](https://unsplash.com/@iakeiohnson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 编码:

编码是存储数据的过程。这就像数据的翻译。不同的计算机系统使用不同形式的编码，就像不同的人使用不同的语言。就像语言有特定的字母一样，编码也有自己的字母。

>>相同的数据可以编码成各种形式。<<

**Base:** 我们可以用来以编码格式表示数据的唯一元素的总数。

# 一些常见的编码格式:

## ASCII 码:美国信息交换标准码

*   每个字符、数字、数字和符号都有一个唯一的 ascii 码。
*   " a-z " = > 97–122
*   " A-Z " = > 65–90
*   “0–9” => 48–57

## **基数 2(二进制)** : 0，1

这是最基本和最广泛使用的编码，所有的计算机和几乎所有的数字微处理器设备都使用这种编码来处理数据和许多事情。

它只有两个唯一的基数或数字，一个是 0，另一个是 1，这就是为什么我们称它为基数 2 编码或二进制。很容易解码。

> `ASCII: This is some ASCII text, and I like it very much.`
> 
> `Binary:01010100 01101000 01101001 01110011 00100000 01101001 01110011 00100000 01110011 01101111 01101101 01100101 00100000 01000001 01010011 01000011 01001001 01001001 00100000 01110100 01100101 01111000 01110100 00101100 00100000 01100001 01101110 01100100 00100000 01001001 00100000 01101100 01101001 01101011 01100101 00100000 01101001 01110100 00100000 01110110 01100101 01110010 01111001 00100000 01101101 01110101 01100011 01101000 00101110`

**解码工具:**[https://www . binaryhexconverter . com/binary-to-ascii-text-converter](https://www.binaryhexconverter.com/binary-to-ascii-text-converter)

## 基数为 16(十六进制):[0–9，a-f]

**Base 16** 编码使用十六进制数字系统(0123456789ABCDEF)对文本进行编码。

> `ASCII: Hey! This is an example of base16 encoding.
> HEX:48657921205468697320697320616E206578616D706C65206F662062617365313620656E636F64696E672E`

**解码工具:**[https://www . rapid tables . com/convert/number/hex-to-ascii . html](https://www.rapidtables.com/convert/number/hex-to-ascii.html)

## 基数 32 : [A-Z，2–7]

**Base 32** 与 base16 编码非常相似，但它有一个更大的字母表，并使用填充字符(等号)。

> `ASCII: Hey! This is an example of base32 encoding.
> Base 32: JBSXSIJAKRUGS4ZANFZSAYLOEBSXQYLNOBWGKIDPMYQGEYLTMUZTEIDFNZRW6ZDJNZTS4===`

**提示:**base32 编码的一些识别特征是填充字符(等号)以及大写字母和数字字母。

这是一个你可以用来编码和解码 base32 的工具:[https://simplycalc.com/base32-encode.php](https://simplycalc.com/base32-encode.php)

# 基数 64:[A-Z，A-Z，0–9，+，/]

Base 64 类似于 base32，但是它有一个更大的字母表！它还使用填充字符。

> `ASCII: Hey! This is an example of base64 encoding.
> Base 64: SGV5ISBUaGlzIGlzIGFuIGV4YW1wbGUgb2YgYmFzZTY0IGVuY29kaW5nLg==`

**提示:**base64 编码的识别特征是大小写字母、数字的使用和消息填充(字符串末尾的等号)。

这是一个可以用来编码和解码 base64 的工具:[https://simplycalc.com/base64-encode.php](https://simplycalc.com/base64-encode.php)

![](img/8ceca98e25b78417aab9340f1b3f193f.png)

[张阳](https://unsplash.com/@iamchang?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# URL 编码(百分比编码)

URL 编码是一种标准，用于对 URL 中的特定数据或字符进行编码。

> `ASCII: Hey! This is an example of URL or Percent Encoding.
> URL encoded: Hey!%20This%20is%20an%20example%20of%20URL%20or%20Percent%20Encoding.%0A`

**提示:**URL 编码的识别特征是使用百分号和一些明文(虽然有 base64 和 base32 URL 编码)。

**编码和解码 URL 或百分比编码的工具:**[https://meyerweb.com/eric/tools/dencoder/](https://meyerweb.com/eric/tools/dencoder/)

## H4ck3r 提示:

你可以使用密码学工具之父:[https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)

H **appy Hacking 黑客。**