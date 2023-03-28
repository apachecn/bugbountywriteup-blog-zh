# Vulnhub:钱箱 1 演练

> 原文：<https://infosecwriteups.com/vulnhub-moneybox-1-walkthrough-fcee571b52ae?source=collection_archive---------3----------------------->

我再次来到这里给你我的另一篇文章(写于 5 个月前！)的箱子从 **vulnhub *钱箱 1*** 。你可以在[Vulnhub:Pwned 1 walk through](https://hellfire0x01.medium.com/vulnhub-pwned-1-walkthrough-af16ad8cdff9)上阅读我的博客，里面有我以前的文章(我以前是怎么写的)。你可以从这里下载这个盒子，

[](https://www.vulnhub.com/entry/moneybox-1,653/) [## 钱箱:1

### 该网站使用“cookies”为您提供最佳、最相关的体验。使用这个网站意味着你对…

www.vulnhub.com](https://www.vulnhub.com/entry/moneybox-1,653/) 

**扫描网络**

使用`netdiscover`我们将找出机器的 IP 地址，

```
sudo netdiscover -r 10.0.2.0/24
```

![](img/e7cca79d89e0cec432e05636ea8284b5.png)

现在我们知道了机器的 IP 地址，我们可能想知道哪些端口是打开的，服务正在运行，等等，我们将通过 nmap 实现这一点，我们将把输出保存在 **nmap.txt** 中。

```
sudo nmap -A -T4 -p- 10.0.2.55 > nmap.txt
```

![](img/68a35474f19995a33623a6630f2f11bb.png)

我们可以看到 3 个端口是开放的，FTP(21)、SSH(22)、HTTP(80)[WEB]服务都在这些端口上运行。

现在，试着通过 whatweb 了解网站上运行着什么技术，

```
whatweb 10.0.2.55
```

![](img/3fd2bee950622b1f1f5c218ed40a61e8.png)

我们没发现什么特别的。

现在我们将使用 **gobuster** 工具暴力破解目录，并将输出保存在 **gobuster.txt** 文件中，

```
gobuster dir http://10.0.2.55/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -x txt,php 2>/dev/null > gobuster.txt
```

![](img/3efe36d7001a765ddd447e31a55e855d.png)

**枚举:**

现在，让我们导航到 [http://10.0.2.55](http://10.0.2.55/) ，我们到达网页，

![](img/bda7de74b547a445c6ba6815d7b45bf6.png)

让我们导航到**/博客**路径，看看是否有适合我们的东西，

![](img/c712c02cf06c835ab7292e48dfec20a5.png)

我检查了它的源代码，发现了这个，

![](img/2accf7baa528039920ca38d47017ca96.png)

让我们导航到 **/S3cr3t-T3xt** 路径，我找到了这个

![](img/5033002567e233da3853b09e9ed6d7b1.png)

但是看看源代码，我们找到了秘钥，

![](img/1339760ae04b7915b2b7ef73accc174d.png)

也许这就是从图像中提取数据的关键。

现在我将通过匿名登录 ftp，

```
ftp 10.0.2.55
```

![](img/74eb18c5ebeee048b4cf1d3909f931ca.png)

我们连上了 ftp。现在我们来列举一下

使用`ls -la`列出目录的所有内容，

![](img/3d3176cb52b23fb4ade092259cdd3bf6.png)

图像被找到，所以我将下载这个图像到我们的本地系统。

使用`get`命令下载该文件，

![](img/81fdd764b485fd97f32ef35cccd89f3f.png)

我将退出 ftp。

现在，在我从秘密目录中找到密钥之前，短语 **3********t4** ( **l33t 代码**)意味着**提取数据**，所以我将尝试使用隐写工具从图像中提取数据(**隐写图**)

```
steghide extract -sf trytofind.jpg
```

![](img/230f3fbea5203e5aabdaa9029d6ab96c.png)

现在正在读取新提取的文件 data.txt 的内容，

![](img/0604d134a7a22675949c21f08139a561.png)

看完这个，我们知道 **renu** 是用户名，消息引用密码太弱，也就是说，我们可以使用 **hydra 工具**破解密码，

```
hydra -l renu -P /usr/share/wordlists/rockyou.txt 10.0.2.55 -t 4 ssh
```

![](img/672eb559eb01045c1b98b4e550e75fee.png)

**剥削:**

唉，我们得到了用户名 **renu** 和密码来认证 SSH，

```
ssh renu@10.0.2.55
```

![](img/83925bdfebdf95e536c458f9e830944b.png)

我们被认证为 renu 用户。现在，用户 1 标志，

执行 **ls** 命令列出目录内容，执行 **cat** 命令读取文本文件内容，

![](img/2c42e6f22a43a7d4c5f2d71e42983e56.png)

我们得到了 user1 标志，现在我将研究如何得到 user2 标志，

做`ls`将列出目录的内容，我发现有 2 个用户，我决定导航到 lily 目录，做`ls -la`，我发现隐藏目录**。ssh** (可以有办法认证我们是用户 lily)，

![](img/e6553331713157514122a32c4a79db19.png)

在**内导航。ssh** 目录，做`ls`，我们得到 **authorized_keys** ，使用`cat`命令读取文件里面的文本，

![](img/0751abc4b7a52262a610a5d575049aec.png)

我们知道我们可以在不知道密码的情况下认证我们是 lily 用户，

```
ssh lily@10.0.2.55
```

![](img/b0c7f111005b9ad1c6ba9609f8c640ba.png)

现在我们是 lily 用户。寻找用户 2 标志，

做`ls`然后`cat`，我们得到了我们的用户 2 标志，

![](img/bc989a64bdef63d11b4c298c51cbbae1.png)

现在，我将运行命令 **sudo -l** ,看看我是否可以在机器上运行命令来获得 root 访问权限，

```
sudo -l
```

![](img/fae7efc6c3985da75f8a7f137148fede.png)

我们可以作为 lily 用户运行命令，

```
sudo perl -e 'exec "/bin/sh";'
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

![](img/9fa08af0146fd812b11f1dcf49efa0a8.png)

使用`cd`导航到根目录并执行`ls -la`，

![](img/c6b9e2b44403987b87cf43c96bb27096.png)

我们有根旗！

抓紧了，我的伙计们，下次再见！再见。