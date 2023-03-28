# 兔年

> 原文：<https://infosecwriteups.com/year-of-the-rabbit-tryhackme-writeup-by-karthikeyan-a3223529e888?source=collection_archive---------5----------------------->

## TryHackMe

## 是时候进入沃伦了…

![](img/e337d97dd1d500ca2da0fd62e12eb85a.png)

# 任务 1:

1.  什么是用户标志？

我们来列举一下机器吧！！！

机器源代码揭秘一页/sup3r_s3cret_fl4g

但是页面被重定向到 Youtube。因此，让我们检查和查看任何可疑的链接

![](img/ff018ea49f2a195ad330e9bc7bbdcb91.png)

我们在这里找到了一个目录/WExYY2Cv-qU

![](img/5ccea15e95962c2f565a19e84d9d792d.png)

让我们下载并使用字符串命令来搜索任何字符串

![](img/55c8dd8553c8c133d229831a22be4766.png)

有一个用户名— **ftpuser** ，密码列表包含实际的 ftp 密码

将密码复制到名为 pass.txt 的文本文件中

![](img/65bece8e1aeede7d5d642e02da2c8b49.png)

```
Use Hydra to Brute force the Password!!
We got the Password!
Now we are In!!
```

![](img/d59093459db4ea9d47f20294493e9dd4.png)

我们从 Ftp 下载的文本文件包含了被称为 BrainFuck 的符号

![](img/6d4df49decf7363c3a1498cdb693b8eb.png)

用[网站](https://www.splitbrain.org/_static/ook/)解码！！

![](img/0cf5b0421b9c594911a9787c9f2e5568.png)![](img/c921dc32a5394eac79b1678ec77ba793.png)

我们拿到证书了

因此，让我们尝试使用上述凭证登录 SSH

![](img/b2a8350b51b125a9bfe417d8158b577a.png)

让我们找到“S3cr3t”

```
find / -name “*s3cr3t*” 2>/dev/null
```

![](img/dea69be7b5903cd4265326e67aecf01c.png)

现在我们得到了 Gwendoline 的密码，让我们登录

![](img/9408ab2075bc83a62a16f7454443e611.png)![](img/f5fcb5e3087ee21ec660b66d34f11953.png)

```
Ans: THM{1107174691af9ff3681d2b5bdb5740b1589bae53}
```

2.根旗是什么？

使用下面的命令并添加**:！文件中的/sh**

```
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
```

![](img/5fcea823ce61402a938c3b1dbba678b3.png)![](img/9abf8fcde91b2ea718c9541a8c318964.png)

我们现在是根了！！！

```
Ans: THM{8d6f163a87a1c80de27a4fd61aef0f3a0ecf9161}
```

感谢您的阅读！！！

黑客快乐！！

```
Author - Karthikeyan N | Cyberw1ng
```

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！