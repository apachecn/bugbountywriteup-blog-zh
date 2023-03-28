# 已提交— TryHackMe 书面报告

> 原文：<https://infosecwriteups.com/committed-tryhackme-b1def8f545e2?source=collection_archive---------5----------------------->

## TryHackMe 已提交房间的书面报告

![](img/c9bc8fe658575b9489d6c19284cdebc6.png)

启动机器并检查提交的文件

![](img/df6e660cee60be9eb186c7e110bddb61.png)

我们可以通过使用 **Python 的 Http 服务器**将这个文件发送到我们的本地机器进行检查

```
**python3 -m http.server**
```

![](img/a7edd81e1aff455d908182815574f8e8.png)

让我们找到攻击机器的 IP 地址

![](img/07768b8a7f269ed2eadfc884310ad6f5.png)

现在让我们在浏览器上使用

```
<machineip>:8000
```

![](img/1e61daaf11e2fd193ac2256c4c612e5e.png)

下载、解压缩并导航到该目录，然后创建一个新文件夹

![](img/fd2210599106f4129074225ffa716c5c.png)

让我们试着用一个叫做[提取器](https://github.com/internetwache/GitTools.git)的工具提取信息

`./extractor.sh . result`

![](img/686b6fa26bd96cf91c5f3484d4d1279d.png)

现在让我们导航到结果文件夹并检查任何信息

![](img/a5d3fd44b8a5f0b285992d8140ba41b3.png)

现在让我们使用 Grep 搜索 Flag

```
grep -iR “flag”
```

![](img/77f99204e78c6d968a3e404a18e9a41b.png)

我们终于拿到了旗子！！！

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！