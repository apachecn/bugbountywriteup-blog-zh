# 深度 CTF 报道第 2 部分

> 原文：<https://infosecwriteups.com/deep-ctf-writeup-part-2-8c2c4c7591bd?source=collection_archive---------1----------------------->

大家好，我们又回到了深度 CTF 的第二部分。让我们从记录开始。

1.  **加密类别**

I)挑战名称—热身

![](img/4821d390d3ffd5d7b367623e0bc8882b.png)

下载文件并打开它。

![](img/eaa3b03c76179018c717411f3e48aa79.png)

该文件包含二进制、十进制、十六进制和八进制编码。所以，把它们分成几部分解码，我们就能拿到旗子了。

**Flag:d33p { Ju5t _ 4 _ n0rm 4l _ CH4 ll _ _ is ` t _ 1t？}**

ii)挑战名称— WTf`ish

![](img/edae8b8930e79a79a9e8a3313cd0aa08.png)

下载文件并查看文本。

![](img/248d62937db39b4207d741898bf75403.png)

这是一种愚蠢的语言。我们来解码吧。

![](img/7dc87dd473167898d8e42650944b2e53.png)

是啊。我们拿到旗子了。

iii)挑战名称—ali3n 再次

![](img/4bcab8976500ee212e3c09ed306afef6.png)

正如他们在《再次挑战外星人》中所说。我认为，这个文件含有外星语言。让我们检查一下。

![](img/400b7362d6389ed210662be615379577.png)

我们来解码吧。使用本站【http://cs.oswego.edu/~dreichel/alienese_decoder/ 。你会拿到旗子的。

**Flag:d33p { POWERRANGERSPDFORCEWON }**

iv)挑战名称— WierdText

![](img/57d334dbd0ba9e5c0d0c15af7fd8ba02.png)

下载文件并查看文本。

![](img/5f6944f3903a6ee8c5dfecf5fec8ba53.png)

这是一些奇怪的文字。但是，他们在寻找新键盘的挑战中给了我们一个提示。让我们找到任何与键盘相关的密码…哦，是的，有…键盘移位密码。

使用它并解码它。

![](img/8362b8b484aeef8dee12d785ade77ada.png)

瞧……！你拿到旗子了伙计。

v)挑战名称— Tr0ll

![](img/2b3f1cbdaeb5c4cf1e51f8acb7b2177d.png)

下载文件并检查文本数据。

![](img/5429e39d1c2583d8515b6f553cae2bd8.png)

它看起来像 base64。我们来解码吧。

![](img/787ef6f2765a87ae496df6c958c8989e.png)

哦，太好了…巨魔..假旗..我们再解码一遍。

![](img/0e94f4220f8c447511e11edf98234e98.png)

再次…巨魔..它是一个小数。我们来解码吧。

![](img/230e388e968d2de232480192e4045501.png)

我们再解码一遍。

![](img/51ed88ecab24e935b0642ebacca109bf.png)

这是 base64。我们来解码吧。

![](img/60bc2a3ae8d26adc7c2433eb22fe02c0.png)

终于……！我们拿到旗子了。

vi)挑战名称— Ali3nCalling

![](img/6d42ff16cef1786d7870aa10d0e6d29a.png)

下载文件并检查 png 文件。

![](img/b4b89a6f829abb71df243a5c4a76e7e2.png)

看起来像猪圈密码。我们来解码吧。

![](img/589b0765eef533bc6cc9f0760e8246b9.png)

是啊。我们轻而易举地拿到了旗子。

vii)挑战名称— RSA 1.0

![](img/5ac0a585efdd75ed460d2613d35b496a.png)

下载文件并查看文件的数据。

![](img/367e56427726a93bada3cba1bb75b290.png)

它拥有 RSA 的所有参数。让我们使用 RSA 工具来获取标志。

![](img/c3ff8b1cdeebba151b04e445a8739048.png)

酷毙了。我们又轻而易举地拿到了旗子。

**2。杂项类别**

I)挑战名称—欢迎

![](img/21815ed35f46075cd2a5444fd9fcde5e.png)

这是一个简单的挑战。只要填写谷歌表格，你就会得到国旗。

**Flag:d33p { w3lc 0m 3 _ to _ d 33 pct f0x 01！！！！！！！}**

ii)挑战名称-我们来聊天吧！

![](img/75824b3166a2c07164a31ea63833d38a.png)

简单说就是加入 discord 服务器。加入它，在公告，你会得到旗帜。

![](img/758b9f30f1bcfc9b6c97a0af79605122.png)

iii)挑战名称-利用事物

![](img/42c2dcd942f121cc3ae55dfaa26a322e.png)

我们只需要找到给定细节的 CVE。

谷歌一下。

![](img/9b213b3cb5eee7846807b35cd7d44e81.png)

**标志:d33p{CVE-2020-8813}**

iv)挑战名称—旧东西

![](img/2f678792f04e20841cf9661b27178438.png)

有一段是说最古老的隐藏方式。复制文本并用谷歌搜索。

![](img/9f5efae4031a5b8170b6c8d46704629b.png)

哦…是凯撒密码。是的，这是最古老的技术之一。

**旗帜:d33p {凯撒}**

v)挑战名称—怪人

![](img/006a46333478c89002bbcac26d058b2f.png)

下载文件并查看文本。

![](img/d0c3ce81f41a7efc925fd173331ebe8c.png)

如我们所知，提示 13–0x-64 意味着 rot 13 →hex → base64。

我们来解码吧。

![](img/e6b18b3a087ed2ea57e9474999dab2c2.png)

我们拿到了魔法。我们来解码吧。

![](img/f6a750818d9c2b6e349db8f598ac1440.png)

我们拿到旗子了。

vi)挑战名称—应该使用 SSH。

![](img/2c179f82bc1acd7f78545fe11e91e763.png)

下载文件。

![](img/b151f05f286f91753160ac12b6acca04.png)![](img/8bb2b67df5cc004588fd13894f316770.png)

我们已经从捕获中提取了文件，我们有 2 个图像:

![](img/59c2ad089cfab5d9366ab303ae03aba9.png)

重命名为 jpg 文件

![](img/3b07523ecc17fb5aace5b23c6bfb961d.png)

我使用 stegcracker 暴力破解密码。

![](img/fb1c6a6beac898374b244706263497a9.png)

密码短语:墨菲

![](img/753e8e7337b3efb434c8f56141338521.png)

**Flag:d33p { f4c3s _ c4n _ B3 _ D3 C3 ptiv 3 }**

**3。网页类别**

I)挑战名——哦 JS！

![](img/ebd24da37a2d220bc9452fd7ba67bfaf.png)

正如挑战名称所说哦 JS！…意味着它与 javascript 有关。让我们检查一下。打开给定的 URL。我们有管理页面。现在，检查源代码。我们发现了一些东西…哦..那是 JS 操。我们来解码吧。

![](img/507f38c542aaa58838564f2de76db3ee.png)![](img/c779d097d9718cada8505dac7d5acb8c.png)![](img/ba9e3dd67e5211868523fc20fa8fd927.png)

我们得到了管理员用户名和密码。通过凭据登录。你会拿到旗子的。

![](img/896dc00bf3d94736dbf00e1f017aed30.png)

ii)挑战名称—魔法单词！

![](img/6ed3b367867af5f114be5287a2ba84d9.png)

打开给定的 URL。

![](img/a250f20458b6a36df6688fe58c58c378.png)

检查源代码。

![](img/b40e4b85f044a736721dd13a1f813bcb.png)

它正在移除 d33p，我们必须使用 magic_word=d33p 来获取标志。我们用 d33d33pp 换 d33p，提交。

![](img/e4925f169f17b5d08eccafe6faef13ce.png)

是的…！我们拿到旗子了。

iii)挑战名称—没有什么是不可能的

![](img/b233ddcc95216e01dc2f461f44a00a8a.png)

打开网址。

![](img/daac1ef991e1bddda0e2bbd1fc593c33.png)

因此，我们在这里有 PHP 编译器，正如描述所说，标志位于/tmp/flag.php。让我们获取标志。

flag.php 的产量很高。所以，我用 base64 编码。

![](img/24e136025263c3bde7aa0720ce88fae4.png)

让我们解码 base64。

![](img/23cc4d950f8d86c16e6524b4737e696e.png)

是啊，我们终于拿到旗子了。

这就是我们的 CTF 深度之旅。我希望，你们喜欢这个。我尽我最大的努力尽可能把文章写得更好。如果你有任何建议或回应。可以放在评论框里。这些都是我身边的人。再见..我们很快会看到新的报道。如果你喜欢，它只是分享它。

作者:

cryptonic 007(【http://instagram.com/cryptonic007】T4)

 [## 登录* Instagram

### 欢迎回到 Instagram。登录查看您的朋友、家人和兴趣爱好捕捉和分享了什么…

instagram.com](http://instagram.com/cryptonic007) 

德雷扬(http://instagram.com/dreycoding/)

 [## 登录* Instagram

### 欢迎回到 Instagram。登录查看您的朋友、家人和兴趣爱好捕捉和分享了什么…

instagram.com](http://instagram.com/dreycoding/) 

埃尔韦([https://twitter.com/Elweth_](https://twitter.com/Elweth_))

[](https://twitter.com/Elweth_) [## 埃尔韦

### 来自 Elweth (@Elweth_)的最新推文:“为@ deepctf 0x 01 https://t.co/OGhaMf46eQ"写文章的第一部分

twitter.com](https://twitter.com/Elweth_)