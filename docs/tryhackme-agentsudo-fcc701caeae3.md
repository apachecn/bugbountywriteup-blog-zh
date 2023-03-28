# Tryhackme: AgentSudo

> 原文：<https://infosecwriteups.com/tryhackme-agentsudo-fcc701caeae3?source=collection_archive---------2----------------------->

## 游戏攻略

# 介绍

乡亲们好，这次让我们根 **AgentSudo** 从 **Tryhackme** 评为易机。

![](img/e77498e5cce5241477cdd6c8505757f1.png)

# 首字母

```
export IP=10.10.220.97
```

## 端口扫描

```
rustscan -a $IP --ulimit 5000 | tee rust.txt
```

发现 3 个开放端口`21, 22, 80`

## nmap

让我们深入挖掘这些港口

```
nmap -sC -sV -p21,22,80 oN nmap $IP -Pn 
```

![](img/5ed2b2c237e604482d4b382eee2f971a.png)

nmap 扫描结果

## 进一步侦察

尝试 FTP 匿名登录→运气不好(不允许)

## 端口 80: http

![](img/c61cc77575c5e87fb5d2647d14a85974.png)

foport 80

在端口 80 上发现此文本，尝试更改用户代理标头→不成功

但是后来，当我换了浏览器，发现了这个，同样的方法也起作用了

![](img/8d349d68610475c94e7b336b37eefd18.png)

有了这个我们知道用户的密码是脆弱的，让我们暴力破解它

## 端口 21: FTP

![](img/21d42e1f8c4949ce89e566dd90953b6f.png)

拿到密码了

![](img/a5326eb6db9d54ecf5cc1742a2960c7f.png)

在 ftp 服务器上找到了三个文件

![](img/97379341f7b61b9ea021904ed89671d3.png)

其中两个是图像，我认为是兔子洞(与上一个相同)XD

![](img/93fe0a1a77d50b38679261154066c821.png)

另一个文件包含注释

![](img/85f15ec0c1ac6c477ebe16d942bf63c8.png)

有了这张纸条，我知道了图片不是:D 的兔子洞

深入挖掘后，我在一张图片中发现了一个 zip 文件

![](img/249e96bae55c0bb5ce4155f9c014a3b6.png)

发现该文件被加密。首先将文件转换成 john 可读的

```
/usr/sbin/zip2john <image_name> > forjhon
```

![](img/7acb6bf5178af0bf2794207fd7e13547.png)

现在用约翰破解它

![](img/c22ee0e457d8a6cf7dffcb0258a585aa.png)

破解后，zip 文件包含另一个注释

![](img/b8eadf9e8e85919e7ea92c336ed39cce.png)

这个奇怪的词很可疑，在 cyberchef.io 上查了一下，发现它是 base64 编码的 XD

![](img/4251870bd7ae1af61355738bb29cfe04.png)

我检查了另一个图像，并用这个作为密码，它的工作！！:o

![](img/2dcd5b08d6af815a368773306f9c595a.png)

同样，该文件包含另一个笔记，(我就像有多少笔记离开 XD)

![](img/fe94eeaec162b7dac0b22296840dbde7.png)

我们在这张纸条上找到了 ssh 密码，耶

# 用户

登录 ssh，我们得到了用户标志

![](img/408b68551800941cd5d840790f01a137.png)

# 根

当你的用户密码是

```
sudo -l
```

![](img/1ca3192bfe8ca2e80c8e9041bfdfcc5f.png)

> 这告诉我们，除了/bin/bash 之外，用户 james 可以以 root 身份运行任何命令
> 
> 实际上有一个 CVE(**CVE-2019–14287**)在这上面命名为“ ***须藤安全绕过*** ”，有趣的部分是我了解了这个 CVE/bug 同一周 XD
> 了解更多关于这个 CVE [**这里**](https://tryhackme.com/room/sudovulnsbypass)

要利用这一点，只需运行命令

```
sudo -u#-1 /bin/bash
```

我们是根

![](img/a11d9f275e4c3ec8075341857ff2bb26.png)

至此，我们完成了这台机器，感谢您抽出时间阅读本博客(网址:

|| [房间](https://tryhackme.com/room/agentsudoctf) || [推特](https://twitter.com/namx05) ||