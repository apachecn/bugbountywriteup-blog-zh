# CTF 纽瓦克学院(NACTF) 2021 —挑战报道

> 原文：<https://infosecwriteups.com/newark-academy-ctf-nactf-2021-challenge-writeups-28ea2e937cfa?source=collection_archive---------2----------------------->

这篇文章包含了在这个 CTF 的一些挑战。

![](img/26c7bb84266d0b7c91bd7d264850ff51.png)

## 1.朱丽叶的笔记

![](img/bd52577feb5c74e7daedaa754b5e9088.png)

这是 CTF 的第一次加密挑战。他们给了我们信息，我们必须解码才能找到旗子。

它是一个简单的整数到字符的匹配。我编了一个小 python 代码来找旗子。我避开了括号，后来加上了。

![](img/aa61d4c2ac6749cff0191fe5ebf55306.png)![](img/150900a4a7e1be0349c8c92614deb94b.png)

朱丽叶笔记的标志

## 2.秘密成分

![](img/9aa196e6945e07662c15c0d9295f315d.png)

这是取证类的第一个挑战。他们给了我们一个文件，是文本文件，扩展名为。txt”。

我使用了`file`来检查标题。

![](img/5890160fa57ad53b5b868c68ad53c57c.png)

它似乎不是一个文本文件，但它是一个图像文件。“jpeg”扩展名。所以把文件复制成 jpeg 格式。

![](img/efc26fa90ec5f6acaea0bc52d2ff83af.png)

后来我打开图像文件，我得到了旗帜。

![](img/447d71a7884167955f1587b81c69a689.png)

秘密配料标志

## 3.力量护身符

![](img/3d869e5e6cf90c5812d60847b922383a.png)

这是一个法医挑战。他们给了一个 jpg 图像文件。当我们打开它时，我们可以看到一幅海洋的图片。我用这个图片试了一下`exiftool`，什么也没找到。

![](img/22a46f6e58d70aa84d72502cd1e29326.png)

然后我试着用`binwalk`来查找是否有任何文件是用这张图片压缩的，并且成功了。所以我提取了文件。

```
binwalk -e ocean.jpg
```

![](img/68d85302f5e2ab929c586eb3cbed818c.png)

深入到提取的文件夹中，有一个 flag.txt 文件，其中包含了 flag。

![](img/5f4d6dbcfd27d375643977fcc4a26f8f.png)

普沃护身符的标志

## 4.在罗马的时候…

![](img/eb823cfabb5af398e4817f6c59cbf6a4.png)

给出的线索是罗马。所以这将是凯撒密码。他们给出了编码文本，我们已经破译了。我用 [dcode](https://www.dcode.fr/caesar-cipher) 解码。

标志是 nactf { * * * * * _ jul * us _ ca * * r }。

## 5.基础

![](img/2c2facd24cdd5600bd2cb5820379bfb7.png)

他们给出了 base64 编码的文本，我们只是解码它。我用了 [cyberchef](https://gchq.github.io/CyberChef) 拿到了旗子。

![](img/400e95ca7439970405c7cd66b45a7479.png)

基地旗帜

## 6.混杂

![](img/76005f9fab9ea4c9ff04accc8b0c6edd.png)

该质询基于 MD5 哈希。我们必须找到散列成给定 MD5 散列的字符串。我用在线的[解密器](https://www.md5online.org/md5-decrypt.html)找到了字符串。

![](img/7d8052325673984c5d641fd2793602fc.png)

哈希标志

7.退伍军人节

![](img/1f0008032dac2d7c3a7776b48989fab6.png)

在挑战的描述中，他们给出的线索是“vigenere ”,这告诉我们它是 Vigenere 密码。所以我们必须解码并拿到旗子。关键是‘美国’，因为他爱它。

![](img/173f1c13356687f521500023760c2cc1.png)

退伍军人节旗帜

8.警告

![](img/bec4199cf2fd3630c877d3c23c90f3c8.png)

给定的“warning.txt”文件在打开时没有任何内容。

我用`binwalk`检查了文件，但是没有任何压缩文件。所以我用`hexdump`来检查十六进制值。我发现了可疑的事情。它有 20 和 9 的多个条目。

![](img/3fdf530c9c5ee9261db2b4fd991bb374.png)

然后我取出这些值，编写了一个 python 脚本，将它们转换成二进制。

![](img/e9994cf95a24f4e05add4d0f4be06746.png)

输出:

```
01110100011010000011001101100011001101000111001001110010001100000111010001110011001100010110111001110100011010000011001101100011011000010110011000110011011101000011001101110010001100010110000100110100011100100011001101110000001100000011000101110011001100000110111000110000011101010111001100111001001110000011000101110101011001000011100100110011011010000011100100110001001100100110100000110011011001010011100000111001
```

然后我把这个二进制文件转换成字符串，这个字符串给出了这个标志。

![](img/3d25c90f65d1aaf386fccb8c7d908afc.png)

警告标志

用 nactf{}包起来。

演奏 CTF 很有趣。它经历了从简单到困难的挑战。希望你学到了新东西。

阅读我的其他文章。

网址:[https://vishnuram1999.github.io/](https://vishnuram1999.github.io/)

更多其他文章请关注我。

[](https://viking71.medium.com/) [## Vishnuram Rajkumar - Medium

### 给定的文本文件包含 n、e 和 c 的值，这是一个 RSA 解密挑战。n =...

viking71.medium.com](https://viking71.medium.com/)